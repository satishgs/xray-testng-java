# xray-testng-java

import okhttp3.*;

import java.io.File;
import java.io.IOException;

public class UploadTestResults {
    public static void main(String[] args) {
        String jiraUrl = "http://your-jira-instance.com";
        String projectKey = "YOUR_PROJECT_KEY";
        String token = "your-PAT-token";
        String filePath = "path-to-your/testng.xml";

        File file = new File(filePath);

        OkHttpClient client = new OkHttpClient().newBuilder()
                .build();
        MediaType mediaType = MediaType.parse("text/xml");
        RequestBody body = new MultipartBody.Builder().setType(MultipartBody.FORM)
                .addFormDataPart("file", "testng.xml",
                        RequestBody.create(file, mediaType))
                .addFormDataPart("projectKey", projectKey)
                .build();
        Request request = new Request.Builder()
                .url(jiraUrl + "/rest/raven/1.0/import/execution/testng")
                .method("POST", body)
                .addHeader("Authorization", "Bearer " + token)
                .build();
        try {
            Response response = client.newCall(request).execute();
            if (response.isSuccessful()) {
                System.out.println("File uploaded successfully!");
            } else {
                System.out.println("Error occurred: " + response.message());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}


import okhttp3.*;

import java.io.File;
import java.io.IOException;
import java.util.concurrent.TimeUnit;

public class UploadTestResults {
    public static void main(String[] args) {
        OkHttpClient client = new OkHttpClient.Builder()
            .connectTimeout(30, TimeUnit.SECONDS)  // Set connection timeout to 30 seconds
            .writeTimeout(30, TimeUnit.SECONDS)   // Set write timeout to 30 seconds
            .readTimeout(30, TimeUnit.SECONDS)    // Set read timeout to 30 seconds
            .followRedirects(true)                // Enable redirects
            .build();

        String jiraUrl = "https://your-jira-instance.atlassian.net";
        String projectKey = "YOUR_PROJECT_KEY";
        String token = "your-PAT-token";
        String filePath = "path-to-your/testng.xml";

        RequestBody requestBody = new MultipartBody.Builder()
            .setType(MultipartBody.FORM)
            .addFormDataPart("file", "testng.xml",
                RequestBody.create(
                    new File(filePath),
                    MediaType.parse("application/xml")))
            .build();

        Request request = new Request.Builder()
            .url(jiraUrl + "/rest/raven/1.0/import/execution/testng?projectKey=" + projectKey)
            .header("Authorization", "Bearer " + token)
            .post(requestBody)
            .build();

        try {
            Response response = client.newCall(request).execute();
            if (response.isSuccessful()) {
                System.out.println("File uploaded successfully!");
            } else {
                System.out.println("Error occurred: " + response.code());
                if(response.body() != null) {
                    System.out.println("Response: " + response.body().string());
                }
            }
        } catch (IOException e) {
            System.out.println("IOException occurred: " + e.getMessage());
        }
    }
}

