# 认证授权

我们选择使用shiro来控制整个系统的认证与授权。

Shiro为我们提供了很多"开箱即用"的功能，但由于我们是无状态接口，我们希望使用JWT来生成传输过程的token并进行验证。

所以我们实现了自己的认证授权过滤器。

## Shiro

我认为我们仍有必要对Shiro的整个流程做一个简单的概述，以此来引入我们系统中是如何自定义实现的。

Shiro是一个安全框架，它主要作用在于简化身份认证和授权。

认证：认证的意义在于给需要登录的用户颁发一个通行证（如token）。只有认证成功的用户（账号密码匹配），才可以获得它

授权：授权是在用户认证成功后，授予认证用户权限，以此控制其可以访问的资源。

### Realm

既然Shiro要进行认证授权，那必然需要数据源，也就是我们认证时的判断依据和授权给用户的资源。

Shiro提供很多中数据源的表现形式。但它提供一个同一的访问接口，Realm。

Realm就是我们操作数据源的抽象接口，我们需要实现它，来完成自己的数据源操作，获取当前的请求是否拥有权限、是否认证通过。

### Subject

用户的每一次请求，Shiro都会将其封装成为一个Subject实体。我们可以通过Subject对当前请求者进行操作，如登录、注销、获取其权限等等。

### CacheManager

缓存控制器，来管理如用户、角色、权限等的缓存的；因为这些数据基本上很少去改变，放到缓存中后可以提高访问的性能

### SecurityManager

SecurityManager管理着所有Subject、且负责进行认证和授权、及会话、缓存的管理。所有的交互，都需要它去调用。

上面所提供的Realm、Subject、CacheManager等，也都需要注册到SecurityManager中供其管理。

## 自定义认证接口

由于我们是REST风格接口，我们提供了一个login接口供用户获取token。并在此接口中显式的为请求用户进行认证。关键代码如下：

```
  /**
     * 登录
     *
     * @param loginReq loginReq
     * @return ResponseEntity
     */
    public ResponseEntity login(LoginReq loginReq) {
        SysUser query = new SysUser();
        query.setAccount(loginReq.getAccount());
        SysUser existUser = sysUserMapper.selectOne(query);
        if (existUser == null) {
            return ErrorResponseEntity.error(AuthError.USER_NOT_EXIST, HttpStatus.NOT_FOUND);
        }
        if (!existUser.getPassword().equals(Utils.md5(loginReq.getPassword()))) {
            return ErrorResponseEntity.error(AuthError.PASSWORD_ERROR, HttpStatus.NOT_FOUND);
        }
        String token = jwtService.createToken(existUser.getId(), 0);
        Subject subject = SecurityUtils.getSubject();
        subject.login(new AuthenticationToken() {
            @Override
            public Object getPrincipal() {
                return token;
            }

            @Override
            public Object getCredentials() {
                return token;
            }
        });
        LoginRes loginRes = new LoginRes();
        loginRes.setToken(token);
        loginRes.setName(existUser.getName());
        return ResponseEntity.ok(loginRes);
    }
```

当我们调用subject.login后，shiro的SecruityManager会寻找我们的Realm，调用其认证方法，查询数据库。

故我们实现了自己的Realm。并实现了其认证方法，关键代码如下：

```
 /**
     * 认证
     *
     * @param authenticationToken 认证token
     * @return 认证信息
     * @throws AuthenticationException 异常
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        try {
            String token = (String) authenticationToken.getCredentials();
            Claims claims = jwtService.parseToken(token);
            if (claims == null) {
                throw new AuthenticationException(BasisErrorCode.TOKEN_INVALID.getDescription());
            }
            String userId = claims.getAudience();
            if (!jwtService.isMultipartTerminal()) {
                String cacheToken = CacheFacade.get(JwtService.JWT_TOKEN_PREFIX + userId);
                if (StringUtils.isEmpty(cacheToken) || !token.equals(cacheToken)) {
                    throw new AuthenticationException(BasisErrorCode.TOKEN_INVALID.getDescription());
                }
            }
            RequestContext requestContext = WebContextFacade.getRequestContext();
            requestContext.setUserId(Long.parseLong(userId));
            WebContextFacade.setRequestContext(requestContext);
        } catch (Exception e) {
            throw new AuthenticationException(BasisErrorCode.TOKEN_INVALID.getDescription());
        }
        return new SimpleAuthenticationInfo(authenticationToken.getPrincipal(), authenticationToken.getCredentials(), getName());
    }
```

## 自定义授权接口

当用户请求资源时，Shiro会对已经通过认证的用户进行授权，判断其是否拥有接口的访问权限。此时SecurityManager会调用Realm的授权接口，获取授权信息。

自定义授权方法关键代码如下：

```
/**
     * 授权
     *
     * @param principalCollection 授权列表
     * @return 授权信息
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        String jwtToken = (String) super.getAvailablePrincipal(principalCollection);
        Claims claims = jwtService.parseToken(jwtToken);
        if (claims == null) {
            throw new AuthenticationException(BasisErrorCode.TOKEN_INVALID.getDescription());
        }
        SysUser user = sysUserService.info(Long.parseLong(claims.getAudience()));
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        if (user == null) {
            return info;
        }

        List<String> permissionCodeList;
        SysUserRole sysUserRoleQuery = new SysUserRole();
        sysUserRoleQuery.setUserId(user.getId());
        //根据用户id，获取当前用户的角色
        List<SysUserRole> sysUserRoleList = sysUserRoleService.list(sysUserRoleQuery);
        boolean isAdmin = sysUserRoleList.stream().anyMatch(userRole -> userRole.getRoleId() == 0);
        info.setRoles(sysUserRoleList.stream().map(userRole -> userRole.getRoleId().toString()).collect(Collectors.toSet()));
        //如果是管理员用户，查询全部权限
        if (isAdmin) {
            permissionCodeList = sysPermissionService.selectAll().stream().map(SysPermission::getCode).collect(Collectors.toList());
        } else {
            //如果不是管理员用户，查询该用户所有角色id
            List<Long> roleIds = sysUserRoleList.stream().map(SysUserRole::getRoleId).collect(Collectors.toList());
            //根据角色id列表查询所有权限id
            List<Long> permissionIds = sysRolePermissionService.selectByRoleIdList(roleIds).stream().map(SysRolePermission::getPermissionId).collect(Collectors.toList());
            //根据权限id列表查询所有权限code
            permissionCodeList = sysPermissionService.selectByIdList(permissionIds).stream().map(SysPermission::getCode).collect(Collectors.toList());
        }
        info.addStringPermissions(permissionCodeList);
        return info;
    }
```

## 自定义缓存管理器

由于每次授权所需要查询的数据过多，故我们基于core包下的CacheFacede实现了自己的缓存管理器。

您可以查看com.github.faster.framework.admin.shiro.cache包下代码。

## 自定义权限过滤器

由于我们需要使用JWT生成认证时的token，所以我们需要弃用Shiro默认的session机制。并且实现我们自己的过滤器。

禁用session代码如下：

```
DefaultSubjectDAO subjectDAO = new DefaultSubjectDAO();
DefaultSessionStorageEvaluator defaultSessionStorageEvaluator = new DefaultSessionStorageEvaluator();
defaultSessionStorageEvaluator.setSessionStorageEnabled(false);
subjectDAO.setSessionStorageEvaluator(defaultSessionStorageEvaluator);
securityManager.setSubjectDAO(subjectDAO);
```


自定义过滤器代码如下：

```
package com.github.faster.framework.admin.shiro;

import com.alibaba.fastjson.JSON;
import com.github.faster.framework.core.exception.model.BasisErrorCode;
import com.github.faster.framework.core.exception.model.ResultError;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.web.filter.authc.AuthenticatingFilter;
import org.apache.shiro.web.util.WebUtils;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.RequestMethod;

import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

/**
 * @author zhangbowen
 */
public class ShiroFilter extends AuthenticatingFilter {

    @Override
    protected AuthenticationToken createToken(ServletRequest servletRequest, ServletResponse servletResponse) throws Exception {
        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
        String jwtToken = httpServletRequest.getHeader(HttpHeaders.AUTHORIZATION);
        return new AuthenticationToken() {
            @Override
            public Object getPrincipal() {
                return jwtToken;
            }

            @Override
            public Object getCredentials() {
                return jwtToken;
            }
        };
    }

    @Override
    protected boolean onAccessDenied(ServletRequest servletRequest, ServletResponse servletResponse) throws Exception {
        return executeLogin(servletRequest, servletResponse);
    }

    @Override
    protected boolean onLoginFailure(AuthenticationToken token, AuthenticationException e, ServletRequest request, ServletResponse response) {
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        httpResponse.setStatus(HttpStatus.UNAUTHORIZED.value());
        httpResponse.setContentType("application/json;charset=utf-8");
        try (PrintWriter printWriter = httpResponse.getWriter()) {
            printWriter.write(JSON.toJSONString(new ResultError(BasisErrorCode.TOKEN_INVALID, HttpStatus.UNAUTHORIZED)));
            printWriter.flush();
        } catch (IOException ignore) {
        }
        return false;
    }

    @Override
    protected boolean preHandle(ServletRequest request, ServletResponse response) throws Exception {
        if (RequestMethod.OPTIONS.name().equals(WebUtils.toHttp(request).getMethod())) {
            HttpServletResponse httpResponse = (HttpServletResponse) response;
            httpResponse.setStatus(HttpStatus.OK.value());
            httpResponse.setHeader(HttpHeaders.ACCESS_CONTROL_ALLOW_ORIGIN, "*");
            httpResponse.setHeader(HttpHeaders.ACCESS_CONTROL_ALLOW_METHODS, "*");
            httpResponse.setHeader(HttpHeaders.ACCESS_CONTROL_ALLOW_HEADERS, "*");
            httpResponse.setHeader(HttpHeaders.ACCESS_CONTROL_ALLOW_CREDENTIALS, "true");
            httpResponse.setStatus(HttpStatus.OK.value());
            return false;
        }
        return super.preHandle(request, response);
    }
}

```