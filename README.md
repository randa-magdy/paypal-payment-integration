# **Payment Integration with PayPal - Documentation**

This document describes the integration of **PayPal payment** in the booking platform for hotel and flight reservations. It explains how payments are processed, tracked, and recorded using the **Strategy design pattern** to support multiple providers in the future.

The documentation includes:

* Overview of payment flows
* Detailed use case scenarios


## **Table of Contents**

1. [Overview](#overview)
2. [Use Case Flows](#use-case-flows)

   * [Actors](#actors)
   * [Payment Flow Scenarios](#payment-flow-scenarios)

     * [1. Payment Completed Successfully](#1-payment-completed-successfully)
     * [2. Payment Failed](#2-payment-failed)
     * [3. Payment Authorized but Not Captured](#3-payment-authorized-but-not-captured)
     * [4. Refund Flow](#4-refund-flow)
     * [5. User Abandons Payment](#5-user-abandons-payment)

---

## **Overview**

The payment integration supports:

* PayPal payment flow (`authorize`, `capture`, `refund`)
* Transaction tracking for each booking
* Webhook handling for real-time payment updates
* Ability to extend to other payment providers

**Key concepts:**

* **Transaction**: Each payment attempt linked to a booking
* **Booking**: Represents hotel/flight reservation
* **Webhook**: Real-time notification from PayPal for  `payment`, `capture`, `refund` events

---

## **Use Case Flows**

### **Actors**

* **Frontend (User Interface)**

  * Displays booking and payment options
  * Initiates payment requests to the backend
  * Redirects the user to PayPal for approval
  * Shows `success`, `failure`, or `cancellation` messages after payment

* **Backend (PaymentController, PaymentService, StrategyFactory, PaymentProvider)**

  * Handles all API requests from the frontend
  * Creates and updates booking records
  * Manages payment transactions
  * Selects the correct payment provider strategy (e.g., PayPal)
  * Calls external APIs (PayPal) and processes webhooks

* **PayPal (External Payment Gateway)**

  * Provides approval UI for the customer
  * Handles authentication and payment authorization/capture
  * Sends webhooks for `order`, `capture`, `authorization`, and `refund` events

* **Database (Persistence Layer)**

  * Stores booking records with current status (`PENDING`, `CONFIRMED`, `COMPLETED`, `CANCELLED`)
  * Stores payment transactions and their lifecycle states (`INITIATED`, `CREATED`, `APPROVED`, `PENDING`, `AUTHORIZED`, `CAPTURED`, `COMPLETED`, `FAILED`, `CANCELLED`, `REFUNDED`, `PARTIALLY_REFUNDED`, `EXPIRED`)
  * Stores refund requests and results
  * Ensures consistency between booking lifecycle and payment lifecycle.

* **Cache Layer (Redis)**

  * Cache access tokens & expiry for PayPal API calls.
