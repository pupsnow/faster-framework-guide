# 异常规范

- 对于接口返回的错误信息，我们需要新建error包，用于存储errorCode
- errorCode应从2000开始（1000到2000为框架错误码）
- 我们不需要手动捕获异常，以有全局异常捕获机制。
- 接口异常我们使用ErrorResponseEntity.error()返回，必须HttpStatus必须大于400。
- 我们尽量靠前返回异常，靠后处理业务逻辑。如下：

    ```
    public ResponseEntity test(Long userId){
        if(userId<10L>){
            return ErrorResponseEntity.error();
        }
        if(userMapper.existUser(userId)){
            return ErrorResponseEntity.error();
        }
        //todo more opreation
    }
    ```

- 异常必须实现ErrorCode接口，示例如下：

    ```
    @AllArgsConstructor
    @Getter
    public enum  RoleError implements ErrorCode {
        ADMIN_CANNOT_DELETE(1120,"超级管理员角色不可删除");
        private int value;
        private String description;
    }
    ```

