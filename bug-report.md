# 📁 Sample QA Documentation Vault

This document contains a production-ready bug report and the corresponding manual test cases used to isolate the defect.

---

## 📊 Part 1: Manual Test Case Suite
**Feature:** User Authentication & Guest Checkout  
**Objective:** Verify checkout behavior for authenticated vs. unauthenticated users when applying discount codes.

- [x] **TC-01:** Verify successful checkout for Logged-In User *without* a promo code. (**PASS**)
- [x] **TC-02:** Verify successful checkout for Logged-In User *with* valid promo code `SAVE10`. (**PASS**)
- [x] **TC-03:** Verify successful checkout for Guest User *without* a promo code. (**PASS**)
- [ ] **TC-04:** Verify successful checkout for Guest User *with* valid promo code `SAVE10`. (**FAIL - See Bug Report Below**)

---

## 🪲 Part 2: Incident Bug Report [BUG-1042]

### 💥 Checkout fails with 500 Error when user applies valid promo code on Guest Checkout

**Status:** Open  
**Priority:** High 🔴  
**Severity:** Critical 🔥  

### 📱 Environment & Testing Matrix
*   **Platform:** E-Commerce Web App (Staging Environment)
*   **OS:** macOS Sonoma 14.4 / Browser: Google Chrome v122
*   **User State:** Unauthenticated (Guest Checkout)

### 📝 Description
When an unauthenticated guest user attempts to complete a purchase after applying a valid 10% off promotion code (`SAVE10`), the checkout process fails. The UI displays a generic "Something went wrong" error banner, the payment processing spinner runs infinitely, and the order is never created.

### 🎬 Steps to Reproduce
1. Navigate to the staging site storefront.
2. Add "Classic Leather Jacket" to the cart.
3. Click **Proceed to Checkout** and select **Checkout as Guest**.
4. Fill in valid shipping and test payment details.
5. Enter promo code `SAVE10` and click **Apply** (Observe: 10% deduction applies).
6. Click the **Place Order** button.

### 📉 Expected Result
Order processes successfully. User is redirected to `/checkout/success` and an Order ID is generated.

### 📈 Actual Result
The page hangs on an infinite loading spinner. A red banner displays: *"An unexpected error occurred."* No order is created.

### 🛠️ Technical Evidence (API Logs)
**Network Tab Request Payload (`POST /v1/orders/charge`):**
```json
{
  "cart_id": "cart_992831a",
  "user_id": null,
  "promo_code": "SAVE10",
  "total_amount": 135.00
}
```

**Network Tab Response:**
```json
{
  "status": "error",
  "code": 500,
  "message": "NullPointerException: Cannot invoke 'User.getId()' because 'user' is null in PromoEngineService.java:142"
}
```
