1. 编译的目标机器设置成 “iOS Device”

2. 点击”Product”–>“Archive”

3. Archive 成功之后，就可以在 Xcode 的 **Organizer** 中看到相应的文件

   1. 可以在help中搜索到`Organizer`

4. 点击 Archive Information 中的 “Distribute App”

5. 选择证书类型

   - **如果您使用的是个人或公司苹果开发者账号($99)，请选择 “Ad Hoc”**

   - **如果您使用的是企业苹果开发者账号($299)，请选择 “Enterprice”**

6. 选择profiles

   1. 目前放在~/cer里

   2. 如果没有。登入[开发者账户](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.apple.com%2Faccount%2F)，然后进入 [Certificates, Identifiers & Profiles](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.apple.com%2Faccount%2Fios%2Fcertificate)

      <img src="https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221013171859601.png" alt="image-20221013171859601" style="zoom:50%;" />

      

7. 点 Next，Xcode 会自动将测试设备的签名信息附加上，并将相应的 IPA 文件导出

8. 将ipa文件上传到[蒲公英上传](https://www.pgyer.com/app/publish)

9. 

   

   