package com.hsbc.managementstudio.config;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Value("${app.version}")
    private String version;

    @Value("${app.supportEmail}")
    private String supportEmail;

    public String getVersion() {
        return version;
    }

    public String getSupportEmail() {
        return supportEmail;
    }
}

package com.hsbc.managementstudio.controller;

import com.hsbc.managementstudio.config.AppConfig;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.HashMap;
import java.util.Map;

@RestController
public class AppController {

    @Autowired
    private AppConfig appConfig;

    @GetMapping("/api/info")
    public ResponseEntity<Map<String, String>> getAppInfo() {
        Map<String, String> response = new HashMap<>();
        response.put("version", appConfig.getVersion());
        response.put("supportEmail", appConfig.getSupportEmail());
        return ResponseEntity.ok(response);
    }
}
app.version=1.0.0
app.supportEmail=support@yourdomain.com
