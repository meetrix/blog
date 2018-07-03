---
title: "Using AWS Amplify with AWS IoT"
permalink: /aws/aws_amplify_with_aws_iot/
excerpt: "Getting things working with AWS IoT."
header:
  overlay_image: /assets/images/AWS/aws-amplify-with-iot.png
  teaser: /assets/images/AWS/aws-amplify-with-iot.png
  overlay_filter: 0.5
last_modified_at: 2018-06-15T15:19:22-04:00
toc: true
author: Jay
---

Recently we started working on realtime notificatons of new latest project `Consult`. We chose MQTT over WebSockets for this. Since we wanted to go for a managed service, we decided to go with AWS IoT Core.
We are using AWS Cognito as our User Management System. Therefore we wanted an Authorization mechanism to 
