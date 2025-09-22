# Zoho Analytics OpenAPI Specification (OAS) v2.0

Welcome to the official repository for **Zoho Analytics OpenAPI Specification (OAS) v2.0**. This repository contains standardized OpenAPI Specification files for Zoho Analytics APIs, designed to help developers integrate Zoho Analytics with their applications using consistent, well-documented API definitions.

---

## Overview

The OpenAPI Specification (OAS) offers a standardized approach to documenting RESTful APIs, detailing endpoints, request/response structures, and authentication mechanisms. This repository provides OAS files for Zoho Analytics APIs, enabling developers to:

- Explore and understand Zoho Analytics API endpoints and their functionality
- Generate SDKs in multiple programming languages
- Streamline integration with third-party applications

---

## Repository Structure

The repository is organized as follows:

- **`v2.0/`**: Includes OAS files for Zoho Analytics APIs version 2.0.
  - `data-api.json`: Data-related API operations.
  - `bulk-api.json`: Bulk data processing APIs.
  - `embed-api.json`: Embedding and visualization APIs.
  - `metadata-api.json`: Metadata management APIs.
  - `modeling-api.json`: Data modeling APIs.
  - `share-api.json`: Resource sharing APIs.
  - `usermanagement-api.json`: User and permission management APIs.
  - Additional resource-specific OAS files.

---

## Getting Started

### 1. Explore OAS Files
1. Browse the `v2.0/` directory in this repository.
2. Choose the OAS file relevant to your needs (e.g., `share-api.json` for sharing operations).
3. Use tools like Swagger UI or Postman to import and visualize these files.

### 2. Generate SDKs
Leverage the OAS files to generate client SDKs in languages such as Python, Java, or Node.js.

#### Steps:
1. Import the desired OAS file into Swagger Hub:
   - Navigate to **Swagger Hub > Design > Import API**.
   - Provide the raw URL of the OAS file from this repository.
2. Go to **Codegen > Client SDK** in Swagger Hub.
3. Choose your preferred programming language and download the SDK.

---

## Authentication

Zoho Analytics APIs use **OAuth 2.0** for authentication. To authenticate your requests:

1. Retrieve your `Client ID` and `Client Secret` from the [Zoho Developer Console](https://api-console.zoho.com).
2. Follow the instructions [here](https://www.zoho.com/analytics/api/v2/authentication.html) to generate a `Refresh Token`.
3. Exchange the refresh token for an access token using Zoho Accounts OAuth endpoint.
4. Include the access token in the header of all API requests:

```http
Authorization: Zoho-oauthtoken <access_token>
```

## Base URL

The base URL for API requests varies depending on your data center. Replace the `url` in `servers` section based on the URL domain that matches your data center.

**Data center-specific base URLs:**

- **US:** `https://analyticsapi.zoho.com`
- **EU:** `https://analyticsapi.zoho.eu`
- **IN:** `https://analyticsapi.zoho.in`
- **AU:** `https://analyticsapi.zoho.com.au`
- **JP:** `https://analyticsapi.zoho.jp`
- **SA:** `https://analyticsapi.zoho.sa`
- **CN:** `https://analyticsapi.zoho.com.cn`
- **CA:** `https://analyticsapi.zohocloud.ca`

**Example:**  
`GET https://analyticsapi.zoho.com/restapi/v2/workspaces`

## Security Schemes Update

When configuring the `iam-oauth2-schema`, make sure to update the **Zoho Accounts domain** according to your data center. This step is essential for the authentication process to work correctly.

To find your domain, refer to the complete list of [Zoho Accounts Domain](https://www.zoho.com/analytics/api/v2/api-specification.html#server-uri).

## Request and Response Format
### Request Format

All API requests must be made over **HTTPS**.  
A typical request includes:

- **Base URL:** The appropriate data center endpoint (see [Base URL](#base-url))
- **HTTP Method:** `GET`, `POST`, `PUT`, or `DELETE`
- **Headers:**
  - `Authorization: Zoho-oauthtoken <access_token>`
  - `Content-Type: application/json` (for POST/PUT requests with a body)
- **Body (if applicable):** JSON payload with request parameters

**Example Request:**

```http
POST https://analyticsapi.zoho.com/restapi/v2/workspaces
Authorization: Zoho-oauthtoken 1000.xxxxxxx.xxxxxx
Content-Type: application/x-www-form-urlencoded

CONFIG={"workspaceName": "Sales Workspace","description": "Workspace for sales analytics"}
```

### Response Format

Zoho Analytics APIs return responses in **JSON format**.  
A successful response includes:

- **`status`** - Indicates success (`success`)
- **`summary`** - Brief description of the action performed
- **`data`** - Contains the response payload

**Example Success Response:**
```http
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8

{
  "status": "success",
  "summary": "Create workspace",
  "data": {
    "workspaceId": "1767024000003145002"
  }
}
```

### Error Format

In case of failure, the API returns an error in **JSON format** with the following details:

- **`status`** - Indicates failure (`failure`)
- **`summary`** - Error type or identifier
- **`data.errorCode`** - Numeric error code
- **`data.errorMessage`** - Detailed error explanation

**Example Error Response:**
```http
HTTP/1.1 400 Bad Request
Content-Type: application/json;charset=UTF-8

{
  "status": "failure",
  "summary": "META_DBNAME_DUPLICATE",
  "data": {
    "errorCode": 7101,
    "errorMessage": "Workspace with the same name exists already. Provide an alternate name"
  }
}
```

---

## **Support**

For any questions or assistance, refer to the [Zoho Analytics API Documentation](https://www.zoho.com/analytics/api/) or contact our support team at [support@zohoanalytics.com](mailto:support@zohoanalytics.com).
