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







import okhttp3.*;

import java.io.File;
import java.io.IOException;
import java.net.CookieManager;
import java.net.CookiePolicy;
import java.util.concurrent.TimeUnit;

public class AuthenticateAndUpload {

    public static void main(String[] args) {
        try {
            CookieManager cookieManager = new CookieManager();
            cookieManager.setCookiePolicy(CookiePolicy.ACCEPT_ALL);

            OkHttpClient client = new OkHttpClient.Builder()
                    .connectTimeout(30, TimeUnit.SECONDS)
                    .writeTimeout(30, TimeUnit.SECONDS)
                    .readTimeout(30, TimeUnit.SECONDS)
                    .followRedirects(true)
                    .cookieJar(new JavaNetCookieJar(cookieManager))
                    .build();

            String jiraUrl = "https://your-jira-instance.atlassian.net";
            String token = "your-PAT-token";
            String projectKey = "YOUR_PROJECT_KEY";
            String filePath = "path-to-your/testng.xml";

            // Authenticate and print cookies
            Request requestAuthenticate = new Request.Builder()
                    .url(jiraUrl + "/rest/api/2/myself")
                    .header("Authorization", "Bearer " + token)
                    .build();

            Response responseAuthenticate = client.newCall(requestAuthenticate).execute();

            if (responseAuthenticate.isSuccessful()) {
                System.out.println("Authenticated successfully!");
                System.out.println("Cookies: " + cookieManager.getCookieStore().getCookies());

                // Upload file
                RequestBody requestBody = new MultipartBody.Builder()
                        .setType(MultipartBody.FORM)
                        .addFormDataPart("file", "testng.xml",
                                RequestBody.create(
                                        new File(filePath),
                                        MediaType.parse("application/xml")))
                        .build();

                Request requestUpload = new Request.Builder()
                        .url(jiraUrl + "/rest/raven/1.0/import/execution/testng?projectKey=" + projectKey)
                        .header("Authorization", "Bearer " + token)
                        .post(requestBody)
                        .build();

                Response responseUpload = client.newCall(requestUpload).execute();
                if (responseUpload.isSuccessful()) {
                    System.out.println("File uploaded successfully!");
                } else {
                    System.out.println("Error occurred: " + responseUpload.code());
                    if (responseUpload.body() != null) {
                        System.out.println("Response: " + responseUpload.body().string());
                    }
                }

            } else {
                System.out.println("Error occurred during authentication: " + responseAuthenticate.code());
                if (responseAuthenticate.body() != null) {
                    System.out.println("Response: " + responseAuthenticate.body().string());
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}


import okhttp3.*;

import java.io.File;
import java.io.IOException;

public class TestNGResultsUploader {

    private static final OkHttpClient CLIENT = new OkHttpClient();

    public static void uploadResults(String testNGResultFilePath, String jiraURL, String projectKey, String bearerToken) throws IOException {
        RequestBody requestBody = new MultipartBody.Builder()
                .setType(MultipartBody.FORM)
                .addFormDataPart("file", "testng-results.xml",
                        RequestBody.create(new File(testNGResultFilePath), MediaType.parse("application/xml")))
                .build();

        Request request = new Request.Builder()
                .url(jiraURL + "/rest/raven/1.0/import/execution/testng?projectKey=" + projectKey)
                .header("Authorization", "Bearer " + bearerToken)
                .post(requestBody)
                .build();

        try (Response response = CLIENT.newCall(request).execute()) {
            if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

            System.out.println(response.body().string());
        }
    }

    public static void uploadEvidence(String issueKey, String evidenceFilePath, String jiraURL, String bearerToken) throws IOException {
        RequestBody requestBody = new MultipartBody.Builder()
                .setType(MultipartBody.FORM)
                .addFormDataPart("file", "evidence.png",
                        RequestBody.create(new File(evidenceFilePath), MediaType.parse("image/png")))
                .build();

        Request request = new Request.Builder()
                .url(jiraURL + "/rest/api/2/issue/" + issueKey + "/attachments")
                .header("Authorization", "Bearer " + bearerToken)
                .header("X-Atlassian-Token", "no-check")
                .post(requestBody)
                .build();

        try (Response response = CLIENT.newCall(request).execute()) {
            if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

            System.out.println(response.body().string());
        }
    }

    public static void main(String[] args) {
        try {
            uploadResults("path_to_testng_results.xml", "https://your-jira-instance.com", "YOUR_PROJECT_KEY", "YOUR_BEARER_TOKEN");
            uploadEvidence("ISSUE_OR_EXECUTION_KEY", "path_to_evidence_file.png", "https://your-jira-instance.com", "YOUR_BEARER_TOKEN");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
version: '3'

services:
  influxdb:
    image: influxdb:latest
    ports:
      - "8086:8086"
    volumes:
      - influxdb-data:/var/lib/influxdb
    environment:
      - INFLUXDB_DB=testdb
      - INFLUXDB_USER=testuser
      - INFLUXDB_USER_PASSWORD=testpass

  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    depends_on:
      - influxdb

volumes:
  influxdb-data:



  
pipeline {
    agent any

    environment {
        TEST_CLASSES_DIR = 'src/test/java/com/example/tests'
        TESTNG_XML_FILE = 'testng.xml'
    }

    stages {
        stage('Discover Test Classes') {
            steps {
                script {
                    def testClasses = findTestClasses(env.TEST_CLASSES_DIR)
                    def testClassChoices = testClasses.collect { it.replace('/', '.').replaceAll('.java', '') }

                    // Create dynamic choice parameter with discovered test classes
                    properties([
                        parameters([
                            choice(name: 'SELECTED_TEST_CLASSES', choices: testClassChoices.join('\n'), description: 'Select test classes to run')
                        ])
                    ])
                }
            }
        }

        stage('Generate TestNG XML') {
            steps {
                script {
                    def selectedTestClasses = params.SELECTED_TEST_CLASSES.split('\n')
                    def testNgXmlContent = generateTestNgXml(selectedTestClasses)

                    writeFile file: env.TESTNG_XML_FILE, text: testNgXmlContent
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    def mvnHome = tool(name: 'Maven', type: 'hudson.tasks.Maven$MavenInstallation').home
                    def mvnCmd = "${mvnHome}/bin/mvn"

                    sh "${mvnCmd} clean test -DsuiteXmlFile=${env.TESTNG_XML_FILE}"
                }
            }
        }
    }
}

def findTestClasses(testDir) {
    def testFiles = []
    findFiles(glob: "${testDir}/**/*.java").each { file ->
        testFiles.add(file.getRelativePath(testDir))
    }
    return testFiles
}

def generateTestNgXml(testClasses) {
    def xmlContent = """
    <!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd" >
    <suite name="TestSuite">
        <test name="Test">
            <classes>
    """

    testClasses.each { className ->
        xmlContent += """
                <class name="${className}" />
        """
    }

    xmlContent += """
            </classes>
        </test>
    </suite>
    """

    return xmlContent
}
