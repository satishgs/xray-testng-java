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
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.testng.ITestListener;
import org.testng.ITestResult;

public class InfluxDBReporter implements ITestListener {

    private static final String INFLUX_DB_URL = "http://host.docker.internal:8086/write?db=testdb";

    private void sendToInfluxDB(ITestResult result, String status) {
        String measurement = "testng_results";
        String testName = result.getName().replace(" ", "_");
        long duration = result.getEndMillis() - result.getStartMillis();

        String data = String.format("%s,testName=%s status=\"%s\",duration=%d",
                                    measurement, testName, status, duration);

        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPost post = new HttpPost(INFLUX_DB_URL);
            post.setEntity(new StringEntity(data));

            client.execute(post);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onTestSuccess(ITestResult result) {
        sendToInfluxDB(result, "SUCCESS");
    }

    @Override
    public void onTestFailure(ITestResult result) {
        sendToInfluxDB(result, "FAILURE");
    }
}
version: '3'

services:
  influxdb:
    image: influxdb:2.0
    ports:
      - "8086:8086"
    volumes:
      - influxdb-data:/var/lib/influxdb2
    environment:
      - DOCKER_INFLUXDB_INIT_MODE=setup
      - DOCKER_INFLUXDB_INIT_USERNAME=testuser
      - DOCKER_INFLUXDB_INIT_PASSWORD=testpass
      - DOCKER_INFLUXDB_INIT_ORG=myorg
      - DOCKER_INFLUXDB_INIT_BUCKET=testdb
      - DOCKER_INFLUXDB_INIT_ADMIN_TOKEN=mytoken

  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    depends_on:
      - influxdb

volumes:
  influxdb-data:


Certainly! Here's an updated version of the write-up with a Problem Statement section:

---

# Enabling Linked Connections in Windows via PowerShell

## Problem Statement

Users may experience issues when trying to write to a network share with elevated permissions, especially when User Account Control (UAC) is enabled. This is typically referred to as the "split-token" issue, where elevated and non-elevated applications do not have the same access to network resources. This article aims to address this problem by enabling the `EnableLinkedConnections` registry setting via PowerShell.

## Overview

This guide describes the use of a PowerShell script to modify a specific registry key that addresses the "split-token" issue. The registry key `EnableLinkedConnections` will be set to `1`, which should help to make network drives accessible to both elevated and standard permissions.

## Pre-Requisites

- Administrative access to the machine
- Backup of important data and registry

## PowerShell Script

Here's the PowerShell script that sets the `EnableLinkedConnections` registry key to `1`.

```powershell
# Set the registry key for EnableLinkedConnections

$registryPath = "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System"
$registryKeyName = "EnableLinkedConnections"
$registryKeyValue = 1

# Check if the registry key already exists
if (Test-Path $registryPath) {
    # Change the value of the registry key
    Set-ItemProperty -Path $registryPath -Name $registryKeyName -Value $registryKeyValue
} else {
    # Create the registry key and set its value
    New-Item -Path $registryPath -Force
    New-ItemProperty -Path $registryPath -Name $registryKeyName -Value $registryKeyValue
}

# Output to confirm the registry key has been set
Write-Host "Registry key $registryKeyName has been set to $registryKeyValue."
```

## Instructions to Run the Script

1. Open PowerShell as an administrator.
2. Copy and paste the script into the PowerShell window.
3. Execute the script by pressing Enter.

## Restart Required

After running the script, a system restart is generally required for the changes to take effect. This ensures that the updated settings are loaded by the operating system.

### Workaround for Restart

While some services can be restarted to apply certain changes, for system-level registry changes like `EnableLinkedConnections`, a system restart is almost always necessary. There are no officially supported methods to apply such changes without a restart.

## Caution

Always backup your registry and important data before making changes to the registry. Incorrect changes can cause serious issues with your system.

---

Feel free to modify this content to fit into your Confluence documentation style and guidelines.


{
  "title": "Selenium Automation Dashboard",
  "panels": [
    {
      "title": "Total Passed Tests",
      "type": "gauge",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "SELECT count(\"status\") FROM \"test_metrics\" WHERE \"status\" = 'pass'"
        }
      ]
    },
    {
      "title": "Total Failed Tests",
      "type": "gauge",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "SELECT count(\"status\") FROM \"test_metrics\" WHERE \"status\" = 'fail'"
        }
      ]
    },
    {
      "title": "Average Execution Time",
      "type": "gauge",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "SELECT mean(\"execution_time\") FROM \"test_metrics\""
        }
      ]
    },
    {
      "title": "Test Success Rate",
      "type": "gauge",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "YOUR_QUERY_FOR_TEST_SUCCESS_RATE"
        }
      ]
    },
    {
      "title": "Time Since Last Failure",
      "type": "stat",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "YOUR_QUERY_FOR_TIME_SINCE_LAST_FAILURE"
        }
      ]
    },
    {
      "title": "Tests By Module/Feature",
      "type": "piechart",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "YOUR_QUERY_FOR_TESTS_BY_MODULE_OR_FEATURE"
        }
      ]
    },
    {
      "title": "Common Errors",
      "type": "table",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "YOUR_QUERY_FOR_COMMON_ERRORS"
        }
      ]
    }
  ]
}


import org.influxdb.InfluxDB;
import org.influxdb.InfluxDBFactory;
import org.influxdb.dto.BatchPoints;
import org.influxdb.dto.Point;
import org.testng.ITestContext;
import org.testng.ITestListener;
import org.testng.ITestResult;

import java.util.concurrent.TimeUnit;

public class EnhancedTestListener implements ITestListener {

    private InfluxDB influxDB;
    private final String dbName = "mydb";
    private final String osType = "Windows"; // Assuming the OS is Windows

    @Override
    public void onStart(ITestContext context) {
        influxDB = InfluxDBFactory.connect("http://localhost:8086", "username", "password");
    }

    @Override
    public void onTestSuccess(ITestResult result) {
        writeMetricsToInfluxDB("pass", result);
    }

    @Override
    public void onTestFailure(ITestResult result) {
        writeMetricsToInfluxDB("fail", result);
    }

    private void writeMetricsToInfluxDB(String status, ITestResult result) {
        String nodeName = System.getenv("NODE_NAME");
        String nodeLabels = System.getenv("NODE_LABELS");
        String triggeredBy = System.getenv("BUILD_USER");

        Point point = Point.measurement("test_metrics")
                .time(System.currentTimeMillis(), TimeUnit.MILLISECONDS)
                .addField("status", status)
                .addField("execution_time", result.getEndMillis() - result.getStartMillis())
                .addField("environment", result.getTestContext().getSuite().getParameter("environment"))
                .addField("test_class", result.getTestClass().getName())
                .addField("test_method", result.getMethod().getMethodName())
                .addField("start_time", result.getStartMillis())
                .addField("end_time", result.getEndMillis())
                .addField("os_type", osType)
                .addField("priority", result.getMethod().getPriority())
                .addField("triggered_by", triggeredBy != null ? triggeredBy : "unknown")
                .addField("node_name", nodeName != null ? nodeName : "unknown")
                .addField("node_labels", nodeLabels != null ? nodeLabels : "unknown")
                .build();

        BatchPoints batchPoints = BatchPoints
                .database(dbName)
                .build();
        batchPoints.point(point);

        influxDB.write(batchPoints);
    }
}



{
  "title": "Enhanced Test Automation Dashboard",
  "panels": [
    {
      "title": "Total Passed Tests",
      "type": "gauge",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "SELECT count(\"status\") FROM \"test_metrics\" WHERE \"status\" = 'pass'"
        }
      ]
    },
    {
      "title": "Total Failed Tests",
      "type": "gauge",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "SELECT count(\"status\") FROM \"test_metrics\" WHERE \"status\" = 'fail'"
        }
      ]
    },
    {
      "title": "Average Execution Time",
      "type": "gauge",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "SELECT mean(\"execution_time\") FROM \"test_metrics\""
        }
      ]
    },
    {
      "title": "Flaky Tests",
      "type": "stat",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "SELECT count(\"status\") FROM \"test_metrics\" WHERE \"is_flaky\" = true"
        }
      ]
    },
    {
      "title": "Longest Running Test",
      "type": "stat",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "SELECT max(\"execution_time\") FROM \"test_metrics\""
        }
      ]
    },
    {
      "title": "Recent Failures",
      "type": "table",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "SELECT last(\"test_name\") FROM \"test_metrics\" WHERE \"status\" = 'fail' GROUP BY \"test_name\""
        }
      ]
    },
    {
      "title": "Success/Failure Ratio",
      "type": "bargauge",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "SELECT count(\"status\") FROM \"test_metrics\" GROUP BY \"status\""
        }
      ]
    },
    {
      "title": "Total Executed Tests",
      "type": "stat",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "SELECT count(\"status\") FROM \"test_metrics\""
        }
      ]
    },
    {
      "title": "Error Types",
      "type": "piechart",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "SELECT count(\"error_type\") FROM \"test_metrics\" WHERE \"status\" = 'fail' GROUP BY \"error_type\""
        }
      ]
    },
    {
      "title": "Skipped Tests",
      "type": "stat",
      "datasource": "InfluxDB",
      "targets": [
        {
          "query": "SELECT count(\"status\") FROM \"test_metrics\" WHERE \"status\" = 'skip'"
        }
      ]
    }
  ]
}
