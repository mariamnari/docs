---
title: COMMUNICATION API
deprecated: false
hidden: false
metadata:
  robots: index
---
## Overview

The Communication API allows you to integrate your systems with WEX's platform, enabling seamless and efficient information exchange with your consumers. This API facilitates the retrieval of messages, notifications, and documents, as well as the management of communication preferences.

## Why use the Communication API?

* This means you can easily share information with your consumers, like important messages, notifications, and even documents, all in a fast and organized way.
* Your consumer portal becomes a one-stop shop: No more searching through emails or different websites! Important messages, notifications, and documents all appear right within your system.
* No more missed messages: Important updates and alerts get sent automatically to your consumers, so everyone stays informed.
* Consumers take control: They can choose how they want to receive information based on their preferences.
* Benefits made easy: Consumers can get updates about their benefits, claims, and accounts without any hassle.
* Growth and innovation: Using our API, you can improve your own products and create a smoother experience for your customers.

## What can the Communication API do?

* **Get messages**: See all the messages and notifications for each consumer.
* **Access documents**: Easily find important documents like account statements and tax forms.
* **Manage preferences**: Let consumers choose how they want to receive notifications (online, paper, email, text).
* **Send a variety of notifications**: Share updates about benefits, accounts, claims, and more.

## Key Features

* **Message Retrieval**: Retrieve messages and notifications for individual consumers, allowing you to display relevant information within your platform.
* **Document Access**: Access important documents such as account statements, tax forms, and denial letters, making them readily available to your consumers.
* **Preference Management**: Enable consumers to customize their notification preferences, including delivery methods (online, paper, or both) and alert channels (email, SMS).
* **Comprehensive Notifications**: Support for a wide range of notifications related to benefits, accounts, claims, and other important events. (Refer to the API Reference for a complete list of notification types, display conditions, and content rules.)

## Glossary

This glossary is designed to help you navigate the Communication API documentation with ease. It provides clear explanations of important terms, ensuring a smooth and efficient learning experience.

* **Message**: A general communication or notification delivered to a consumer through the WEX platform.
* **Notification**: A specific type of message that alerts consumers about important events, updates, or changes related to their benefits or accounts.
* **Document**: An electronic file, such as an account statement, tax form, or denial letter, that provides detailed information to consumers.
* **Preference Management**: The ability for consumers to customize how they receive communications, including delivery methods (online, paper) and alert channels (email, SMS).
* **Delivery Methods**: The ways in which notifications and documents can be delivered to consumers, such as online through the portal or as paper copies by mail.

## Message

The Message Endpoints within the Communication API enable you to retrieve and manage messages associated with your consumers.

### Endpoints

`GET /messages`

* Retrieves a list of messages associated with a specified consumer. Use this endpoint to fetch a summary of messages for a consumer. You can filter messages by various criteria (for example, type, date range) to display the most relevant information in your application's dashboard or notification center.

`GET /message`

* Retrieves a specific message identified by the given message ID for the specified consumer. Use this endpoint to retrieve the full content of a specific message when a consumer interacts with a message preview or notification in your application. This allows you to display the complete message details to the consumer.

`PUT /message`

* Updates the status of a specific message for the specified consumer. Use this endpoint to mark a message as read or unread within your application. This allows you to synchronize the message status between your system and the WEX platform, ensuring consistency for the consumer.

### Example Scenario

Imagine a consumer logs into your benefits portal. You can use the GET /messages endpoint to retrieve a summary of their unread messages and display them in a notification widget. When the consumer clicks on a specific message, you can use the GET /message endpoint to fetch the full message content and display it in a pop-up window.

### Usage Example

Click 'Open Recipe' below to see a usage example of how to retrieve a message.

## Document

The Document Endpoint within the Communication API enables you to retrieve a specific document available to your consumers.

### Endpoint

`GET /document`

* Retrieves a specific document identified by the given document ID. Use this endpoint to fetch and display a document associated with a message or notification.

### Example Scenario

Imagine a consumer logs into your benefits portal. They see a notification: "New Account Statement Available!" Curious, the consumer clicks the notification.

Behind the scenes, your platform uses the GET /document endpoint to instantly retrieve the statement from WEX's system. You can then seamlessly display the statement within your portal, without needing to redirect the consumer to another page or application.
Usage Example
Click 'Open Recipe' below to see a usage example of how to retrieve a document.

## Preferences

The Preferences Endpoint within the Communication API enables you to retrieve the notification preferences specific to your consumers.

### Endpoints

`GET /preferences`

* Retrieves the notification preferences for the specified consumer. Use this endpoint to pre-fill a preferences form in your application, allowing consumers to review and update their existing notification settings.

`PUT /preferences`

* Updates the notification preferences for the specified consumer. Use this endpoint to save changes to a consumer's notification preferences. Ensure your application validates the input according to the rules and constraints defined in the API reference.
* Rules for PUT:
  * Consumers can choose their preferred delivery method (online, paper, or both) for certain notifications.
  * Consumers can enable or disable email and SMS alerts for specific notification types.
  * Some notification preferences may be restricted based on employer settings or account type.

### Example Scenario


A consumer updates their preferences: In your benefits portal, they go to their notification settings. They check the box to receive alerts via text message and uncheck the box for email alerts.


Your system sends the changes: Your system uses the PUT /preferences endpoint to send the new preferences to WEX. It includes the consumer's unique employee ID and the updated settings (text alerts = ON, email alerts = OFF).


WEX updates the consumer's profile: WEX receives the request and updates the consumer's notification preferences accordingly.

### Usage Example


Click 'Open Recipe' below to see a usage example of how to retrieve preferences.
