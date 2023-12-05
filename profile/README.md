# Late-Day Tokens Marketplace

Welcome to the Late-Day Tokens Marketplace project, a microservices-based e-commerce app for handling orders, payments, inventory, and delivery. This project focuses on managing transactions across various services, exploring scalability, testing, and monitoring.

## Overview

The Late-Day Tokens Marketplace is a marketplace specifically designed for late-day tokens. The project involves building a straightforward e-commerce app using a microservices architecture to handle orders, payments, inventory, and delivery. Although a frontend is not mandatory, rigorous testing of the application is required. The testing code should be maintained in a separate Git repository.

For simplicity, the user service is not included in this project. It is assumed that the user specified during order creation is legitimate. Requests should contain instructions on potential failure scenarios during order processing across services, and mechanisms for handling and reverting these failures appropriately. OpenTelemetry is used to collect the required logs across all services.

## Project Conditions

### Payment Service Conditions

The payment service operates under the following conditions:

- New users will have some initial credits.
- If the user exists, credits will be deducted based on the price of the purchase.
- Possible outcomes of the payment service include SUCCESS, INSUFFICIENT_FUND, TIMEOUT, and UNKNOWN.
- If a step is reverted, the funds should be returned to the user account.

## Key Features

- **Microservices Architecture**: The project is built using a microservices architecture to handle distinct functionalities seamlessly.
- **Thorough Testing**: The application undergoes comprehensive testing to ensure robustness and reliability. The testing code is maintained in a separate Git repository.
- **Failure Scenario Handling**: Clear instructions and mechanisms are provided for handling potential failure scenarios during order processing across services.
- **OpenTelemetry Integration**: OpenTelemetry is used to collect logs across all services, facilitating effective monitoring and debugging.


