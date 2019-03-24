#### mysql的用户添加，删除，以及用户权限的授权和回收
1. 添加用户

   >** create user 'robin'@'localhost' identified by 'robin';**

2. 删除用户
    >**drop user 'robin'@'localhosy';**

3. 授权权限
>**grant all on 数据库名.表 to 'robin'@'localhosy'; **
>
grant后面是授权的权限，有select create insert into drop 等 ，如果要授予全部权限的话就用all。*on*后面是数据库或者数据库下的表。*to*后面是用户。如果在用户名的后面加上with grant option **表示这个用户也可以给其他用户授权**。

4. 回收权限
>**revoke  all on 数据库名.表 from 'robin'@'localhosy'; **
>
>和授权的基本一样。

5. 修改用户的密码
>**set passowrd for 'robin'@'localhosy' = password('newpassword');**
>
>也可以通过更新user表修改

6. 刷新权限
>**flush privileges;**