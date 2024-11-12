---
title: "Automating Stock Updates to Salesforce with Node.js and the Pub/Sub API"
date: 2024-11-11 15:00:00
categories: [Deployment, CI/CD]
tags: [Node.js, salesforce, pub/sub] # TAG names should always be lowercase
---

# Automating Stock Updates to Salesforce with Node.js and the Pub/Sub API: A Real-Time Publisher Solution

In this blog post, we discover how to automate stock price updates from the Nairobi Stock Exchange to Salesforce using Node.js and the Salesforce Pub/Sub API. This Node.js application leverages the "publish" capabilities of Pub/Sub to send timely stock data as custom events, ensuring our Salesforce org stays up-to-date with hourly updates in either single-stock or bulk modes.

I built this to test out the interaction between a Salesforce org and an external app as the publisher.

Also because I would like to keep an eye on the Nairobi Stock Exchange, and set up analysis and email/notification actions in a future blog.

---

## Introduction to the Salesforce Stock Price Publisher

The **Salesforce Stock Price Publisher** is a **Node.js application** that does precisely this: it automates stock price fetching from the Nairobi Stock Exchange (or any stock exchange of your choice) and publishes these prices as custom events in Salesforce. It operates with flexibility, allowing you to choose whether to fetch a single stock or a full batch of available stocks, and can be configured for both development and production environments.

---

## Key Features of the Publisher

This Node.js application is designed to be reliable and versatile. Here’s a quick overview of its main features:

- **Automatic, hourly stock price fetching** from the Nairobi Stock Exchange.
- **Configurable data fetching** for either a single stock or all available stocks.
- **Real-time data publishing** to Salesforce via the Pub/Sub API.
- **Environment-specific configuration** for development and production.
- **Secure and robust** authentication for Salesforce.

![Desktop View](/assets/img/posts/nodeJs_to_salesforce.png){: w="700" h="500" }
_Data flow from NodeJS app to Salesforce_

---

## Prerequisites

Before getting started with the Salesforce Stock Price Publisher, make sure you have the following:

1. **Node.js** (version 14 or higher)
2. **Salesforce organization** with Pub/Sub API enabled
3. **RapidAPI account** with access to the Nairobi Stock Exchange API
4. **Salesforce Connected App** (specifically for production deployment)

---

## Setting Up the Application

> To follow along, you can view the repository [ here ](https://github.com/brave-hawk/stock-price-pub-sub)
{: .prompt-tip }

Let’s get started with setting up the application on your system. This process is straightforward and involves installing the required dependencies and configuring environment variables.

1. **Clone the repository:**

   ```bash
   git clone https://github.com/brave-hawk/stock-price-pub-sub.git
   cd stock-price-pub-sub
   ```

2. **Install dependencies:**

   ```bash
   npm install
   ```

3. **Configure environment variables:** Create a `.env` file in the root directory and include these variables:

   ```env
   # Environment
   NODE_ENV=DEV  # or PROD

   # Fetch Mode
   FETCH_MODE=SINGLE  # or ALL

   # Salesforce Credentials
   SALESFORCE_LOGIN_URL=your-salesforce-login-url
   SALESFORCE_USERNAME=your-username
   SALESFORCE_PASSWORD=your-password
   SALESFORCE_TOKEN=your-security-token

   # Production Only (OAuth JWT)
   SALESFORCE_JWT_CLIENT_ID=your-connected-app-client-id
   SALESFORCE_PRIVATE_KEY_FILE=path/to/private-key.key

   # RapidAPI Configuration
   RAPID_API_KEY=your-rapid-api-key
   RAPID_API_HOST=nairobi-stock-exchange-nse.p.rapidapi.com
   ```

   To get your security token (`SALESFORCE_TOKEN`) for the development run, you can follow the instructions to
   [ Reset Security Token ](https://help.salesforce.com/s/articleView?id=xcloud.user_security_token.htm&type=5)

---

## Running the Application

With everything set up, you’re ready to start the application. Here’s how:

```bash
node index.js
```

The `FETCH_MODE` environment variable dictates the mode:

- **SINGLE**: Fetches data for a single stock (e.g., "GLD").
- **ALL**: Fetches data for all available stocks.

![Desktop View](/assets/img/posts/nodeJS_publish_single_stock.png){: w="700" h="500" }
_Screenshot of the command line output when the app is fetching data for stocks and publishing to Salesforce_

---

## Application Architecture

This application is well-structured, with modular components that handle different aspects of data fetching and publishing.

### Main Components

1. **index.js**: The entry point for the application, which schedules data fetching and handles the publishing process.
2. **utils/connectClient.js**: Manages Salesforce Pub/Sub API client connections, supporting both development (username-password) and production (OAuth JWT) authentication.
3. **utils/fetchStockData.js**: Handles API calls to the Nairobi Stock Exchange and provides methods for single or bulk stock data fetching.

---

## Salesforce Integration

Stock data is published as custom Salesforce events in the `HourlyStockEvent__e` channel. The following fields are included:

- **CreatedDate**: Event timestamp
- **CreatedById**: Salesforce User ID
- **Symbol\_\_c**: Stock symbol
- **Price\_\_c**: Current stock price
- **Change\_\_c**: Price change
- **StockName\_\_c**: Name of the stock
- **Volume\_\_c**: Trading volume
- **All_Stock_String\_\_c**: JSON string of all stocks data (when in ALL mode)

![Desktop View](/assets/img/posts/Hourly_stock_event_platformEvent.png){: w="700" h="500" }
_Salesforce platform event showing stock data fields_

---

## Production Deployment

For a production environment:

1. Set `NODE_ENV=PROD` in your `.env` file.
2. Configure a **Salesforce Connected App** with JWT authentication.
3. Generate and store your **private key** securely.
4. Update all production-specific environment variables.
5. Implement monitoring and logging for better tracking of application behavior.

> Watch this space for a blog post coming soon on how to set up the Connected App and generate the key pair needed (as soon as I have time off work)
{: .prompt-info }

---

## Development Tips

During development, you can set `NODE_ENV=DEV` for easier authentication. This mode allows faster iteration, especially when testing data fetching.

---

## Error Handling

The application has built-in error handling for:

- API connection issues
- Salesforce authentication failures
- Data fetching errors

---

## Conclusion

The Salesforce Stock Price Publisher makes it easy to automate stock price updates to Salesforce. With flexible configuration, reliable authentication, and secure data handling, this solution is ideal for businesses needing timely stock data in Salesforce.

---

### Special Thanks

A big thank you to [@iancenry](https://github.com/iancenry) for providing the free Nairobi Stock Exchange API on RapidAPI, and to [@pozil](https://github.com/pozil) for the Pub/Sub API client.

---
