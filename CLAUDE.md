# 项目名称
vibecoding

## 项目概述
Spring Boot 4.1.0 + Java 17 初始项目，Maven 构建，尚无业务逻辑。

## 技术栈
- 后端：Spring Boot 4.1.0 / Spring Framework 7.x
- 构建：Apache Maven + mvnw wrapper
- 测试：JUnit 5 + Spring Boot Test
- Java 版本：17

## 项目结构
```
src/
├── main/
│   ├── java/com/zzw/vibecoding/vibecoding/
│   │   └── VibecodingApplication.java       # @SpringBootApplication 入口
│   └── resources/
│       └── application.properties           # Spring Boot 配置
└── test/
    └── java/com/zzw/vibecoding/vibecoding/
        └── VibecodingApplicationTests.java  # 上下文加载测试
```

## 编码规范
- 包命名：com.zzw.vibecoding.vibecoding.*
- 类文件使用 PascalCase
- 方法 / 变量使用 camelCase
- API 返回统一格式：{ code: int, message: string, data?: any }
- 集成测试使用 @SpringBootTest

## 当前开发状态
-   项目初始化完成
-   Spring Boot 基础框架搭建完成
-   尚无业务模块
-   Web / Controller 层待添加

## 注意事项
- 构建产物 target/，已 gitignore
- 通过 ./mvnw 构建，无需本地安装 Maven
- .env 等敏感文件不要提交到 Git
- 所有新功能先创建 Git 分支再开发
