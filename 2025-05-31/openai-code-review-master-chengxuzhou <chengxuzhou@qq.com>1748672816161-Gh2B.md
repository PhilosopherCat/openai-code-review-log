# 小周项目：OpenAi 代码评审
### 😀代码评分：80
#### 😀代码逻辑与目的：
该代码片段是一个GitHub Actions工作流配置文件，用于构建和运行基于远程JAR的OpenAi代码审查。它还包括一个模型枚举类，用于定义不同的模型，以及一个服务实现类，用于处理代码审查的逻辑。同时，还有一个工具类用于生成访问令牌。

#### 🎯问题点：
1. 工作流配置过于宽松，允许所有分支的push和pull request触发构建，可能引入不必要的构建。
2. 模型枚举中添加了新的模型DEEPSEEK_CHAT，但在服务实现中未使用，造成代码冗余。
3. BearerTokenUtils类中注释的代码片段未注释完全，可能影响代码可读性。

#### 🎯修改建议：
1. 限制工作流只对特定的分支进行构建，例如仅master分支。
2. 删除未使用的模型枚举DEEPSEEK_CHAT。
3. 完整注释掉不再使用的代码片段。

#### 💻修改后的代码：
```yaml
diff --git a/.github/workflows/main-remote-jar.yml b/.github/workflows/main-remote-jar.yml
index 38c4c8a..7af5f9a 100644
--- a/.github/workflows/main-remote-jar.yml
+++ b/.github/workflows/main-remote-jar.yml
@@ -3,10 +3,10 @@ name: Build and Run OpenAiCodeReview By Main Remote Jar
 on:
   push:
     branches:
-      - master
+      - 'master'
   pull_request:
     branches:
-      - master
+      - 'master'
 
 jobs:
   build:
@@ -59,7 +59,7 @@ jobs:
         run: java -jar ./libs/openai-code-review-sdk-1.0.jar
         env:
           # Github 配置；GITHUB_REVIEW_LOG_URI「https://github.com/xfg-studio-project/openai-code-review-log」、GITHUB_TOKEN「https://github.com/settings/tokens」
-          GITHUB_REVIEW_LOG_URI: ${{ secrets.CODE_REVIEW_LOG_URI }}
+          GITHUB_REVIEW_LOG_URI: ${{ secrets.CODE_REVIEW_LOG_URI }} #https://github.com/PhilosopherCat/openai-code-review-log
           GITHUB_TOKEN: ${{ secrets.CODE_TOKEN }}
           COMMIT_PROJECT: ${{ env.REPO_NAME }}
           COMMIT_BRANCH: ${{ env.BRANCH_NAME }}
diff --git a/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/domain/model/Model.java b/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/domain/model/Model.java
index de1bdb9..13512d3 100644
--- a/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/domain/model/Model.java
+++ b/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/domain/model/Model.java
@@ -20,7 +20,10 @@ public enum Model {
     GLM_4V("glm-4v","根据输入的自然语言指令和图像信息完成任务，推荐使用 SSE 或同步调用方式请求接口"),
     GLM_4_FLASH("glm-4-flash","适用简单任务，速度最快，价格最实惠的版本，具有128k上下文"),
     COGVIEW_3("cogview-3","根据用户的文字描述生成图像,使用同步调用方式请求接口"),
+
+    // DEEPSEEK_CHAT("deepseek-chat", "适用于对图像生成、图像描述、图像风格转换、图像修复、图像生成等场景")
     ;
+
     private final String code;
     private final String info;
 
diff --git a/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/domain/service/impl/OpenAiCodeReviewService.java b/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/domain/service/impl/OpenAiCodeReviewService.java
index 0fb4eaf..729ff97 100644
--- a/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/domain/service/impl/OpenAiCodeReviewService.java
+++ b/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/domain/service/impl/OpenAiCodeReviewService.java
@@ -30,6 +30,7 @@ public class OpenAiCodeReviewService extends AbstractOpenAiCodeReviewService {
     protected String codeReview(String diffCode) throws Exception {
         ChatCompletionRequestDTO chatCompletionRequest = new ChatCompletionRequestDTO();
         chatCompletionRequest.setModel(Model.GLM_4_FLASH.getCode());
+       // chatCompletionRequest.setModel(Model.DEEPSEEK_CHAT.getCode());
         chatCompletionRequest.setMessages(new ArrayList<ChatCompletionRequestDTO.Prompt>() {
             private static final long serialVersionUID = -7988151926241837899L;
 
diff --git a/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/types/utils/BearerTokenUtils.java b/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/types/utils/BearerTokenUtils.java
index 41eaf43..b9702f7 100644
--- a/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/types/utils/BearerTokenUtils.java
+++ b/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/types/utils/BearerTokenUtils.java
@@ -36,7 +36,7 @@ public class BearerTokenUtils {
     public static String getToken(String apiKey, String apiSecret) {
         // 缓存Token
         String token = cache.getIfPresent(apiKey);
-        if (null != token) return token;
+        if (null != token) {return token;}
         // 创建Token
         Algorithm algorithm = Algorithm.HMAC256(apiSecret.getBytes(StandardCharsets.UTF_8));
         Map<String, Object> payload = new HashMap<>();
```