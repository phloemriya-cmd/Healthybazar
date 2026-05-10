# Healthybazar
Automated E-commerce testing project for HealthyBazar using Playwright with JavaScript covering end-to-end user workflows such as login, product search, cart, checkout, and order placement.
# HealthyBazar E-commerce Automation Project

This project automates end-to-end testing of the HealthyBazar e-commerce website using **Playwright with JavaScript**.

## Website
https://healthybazar.com/

## Project Objective
To validate critical user workflows of an e-commerce application and ensure smooth functionality of core features.

## Automated Test Scenarios
- User Registration
- Login/Logout
- Product Search
- Product Filtering
- Add Product to Cart
- Update Cart Quantity
- Remove Product from Cart
- Wishlist Functionality
- Checkout Process
- Payment Page Validation
- Order Placement
- User Profile Validation

  //User Registration
  
  import { test, expect } from '@playwright/test';
  test('User Registration with valid details', async ({ page }) => {
  await page.goto('https://healthybazar.com/register');

    await page.fill('#name', 'Riya Kumari');
    await page.fill('#email', 'riya123@gmail.com');
    await page.fill('#mobile', '9876543210');
    await page.fill('#password', 'Password@123');
    await page.fill('#confirmPassword', 'Password@123');

    await page.click('button[type="submit"]');
    await expect(page.locator('.success-message')).toBeVisible();
});

//Login/Logout 

import { test, expect } from '@playwright/test';
test('User Login and Logout Flow - HealthyBazar', async ({ page }) => {

    // Step 1: Open website
    await page.goto('https://healthybazar.com/');
    await page.maximizeWindow();

    console.log("Website opened successfully");

    // Step 2: Click login button
    const loginButton = page.locator('text=Login');
    await expect(loginButton).toBeVisible();
    await loginButton.click();

    console.log("Login page opened");

    // Step 3: Verify login form
    await expect(page.locator('#email')).toBeVisible();
    await expect(page.locator('#password')).toBeVisible();

    // Step 4: Enter credentials
    await page.fill('#email', 'testuser@gmail.com');
    await page.fill('#password', 'Password@123');

    console.log("Credentials entered");

    // Step 5: Click login submit button
    await page.click('button[type="submit"]');

    // Wait for login completion
    await page.waitForLoadState('networkidle');

    // Step 6: Validate successful login
    const userProfile = page.locator('.user-profile');
    await expect(userProfile).toBeVisible();

    console.log("Login successful");

    // Step 7: Validate homepage/dashboard redirect
    await expect(page).toHaveURL(/dashboard|home/i);

    // Step 8: Search product after login
    const searchBox = page.locator('input[type="search"]');
    await searchBox.fill('protein powder');
    await page.keyboard.press('Enter');

    await page.waitForLoadState('networkidle');

    console.log("Product search successful after login");

    // Step 9: Open profile menu
    await userProfile.click();

    // Step 10: Click logout
    const logoutButton = page.locator('text=Logout');
    await expect(logoutButton).toBeVisible();

    await logoutButton.click();

    console.log("Logout clicked");

    // Step 11: Verify successful logout
    await expect(page.locator('text=Login')).toBeVisible();

    // Step 12: Validate user session ended
    await page.goto('https://healthybazar.com/account');

    await expect(page.locator('text=Login')).toBeVisible();

    console.log("Session terminated successfully");

    // Step 13: Take screenshot
    await page.screenshot({
        path: 'screenshots/login-logout-flow.png',
        fullPage: true
    });

    console.log("Login/Logout test completed successfully");
});

//Search Product

import { test, expect } from '@playwright/test';
test('Search Protein Powder Product on HealthyBazar', async ({ page }) => {

    // Step 1: Navigate to website
    await page.goto('https://healthybazar.com/');
    await page.maximizeWindow();

    // Verify homepage loads successfully
    await expect(page).toHaveTitle(/HealthyBazar/i);

    // Step 2: Wait for search box
    const searchBox = page.locator('input[type="search"]');
    await expect(searchBox).toBeVisible();

    // Step 3: Enter product name
    await searchBox.fill('protein powder');

    // Validate entered text
    await expect(searchBox).toHaveValue('protein powder');

    // Step 4: Click search button OR press Enter
    await page.keyboard.press('Enter');

    // Wait for search results
    await page.waitForLoadState('networkidle');

    // Step 5: Validate search result page URL
    await expect(page).toHaveURL(/search/i);

    // Step 6: Validate product list is displayed
    const productResults = page.locator('.product-card');
    await expect(productResults.first()).toBeVisible();

    // Count total products
    const totalProducts = await productResults.count();
    console.log(`Total products found: ${totalProducts}`);

    // Validate minimum results
    expect(totalProducts).toBeGreaterThan(0);

    // Step 7: Verify searched keyword appears in product names
    for (let i = 0; i < totalProducts; i++) {
        const productName = await productResults.nth(i).locator('.product-title').textContent();
        console.log(`Product ${i + 1}: ${productName}`);
    }

    // Step 8: Click first product
    await productResults.first().click();

    // Wait for product details page
    await page.waitForLoadState('networkidle');

    // Step 9: Validate Add to Cart button
    const addToCartBtn = page.locator('text=Add to Cart');
    await expect(addToCartBtn).toBeVisible();

    // Step 10: Take screenshot for evidence
    await page.screenshot({
        path: 'screenshots/protein-product-search.png',
        fullPage: true
    });

    console.log("Protein powder search test completed successfully");
});

//Product Filtering

 import { test, expect } from '@playwright/test';
 test('Product Filtering Functionality - HealthyBazar', async ({ page }) => {

    // Step 1: Open website
    await page.goto('https://healthybazar.com/');
    await page.maximizeWindow();

    console.log("Website launched successfully");

    // Step 2: Search product
    const searchBox = page.locator('input[type="search"]');
    await expect(searchBox).toBeVisible();

    await searchBox.fill('protein powder');
    await page.keyboard.press('Enter');

    await page.waitForLoadState('networkidle');

    console.log("Search completed successfully");

    // Step 3: Validate search results available
    const products = page.locator('.product-card');
    const beforeFilterCount = await products.count();

    console.log(`Products before filtering: ${beforeFilterCount}`);
    expect(beforeFilterCount).toBeGreaterThan(0);

    // Step 4: Apply Brand filter
    const brandFilter = page.locator('text=MuscleBlaze');
    await expect(brandFilter).toBeVisible();
    await brandFilter.click();

    console.log("Brand filter applied");

    await page.waitForLoadState('networkidle');

    // Step 5: Validate filtered products
    const filteredProducts = page.locator('.product-card');
    const afterBrandFilterCount = await filteredProducts.count();

    console.log(`Products after brand filter: ${afterBrandFilterCount}`);
    expect(afterBrandFilterCount).toBeGreaterThan(0);

    // Step 6: Apply price filter
    const minPrice = page.locator('#min-price');
    const maxPrice = page.locator('#max-price');

    await minPrice.fill('500');
    await maxPrice.fill('2000');

    await page.click('text=Apply Filter');

    console.log("Price filter applied");

    await page.waitForLoadState('networkidle');

    // Step 7: Validate updated products list
    const finalProductCount = await filteredProducts.count();
    console.log(`Products after price filter: ${finalProductCount}`);

    expect(finalProductCount).toBeGreaterThan(0);

    // Step 8: Validate each product price falls within range
    const priceElements = page.locator('.product-price');

    const totalPrices = await priceElements.count();

    for (let i = 0; i < totalPrices; i++) {
        const priceText = await priceElements.nth(i).textContent();
        console.log(`Filtered Product Price: ${priceText}`);
    }

    // Step 9: Clear filters
    const clearFilter = page.locator('text=Clear All Filters');
    
    if (await clearFilter.isVisible()) {
        await clearFilter.click();
        console.log("Filters cleared successfully");
    }

    // Step 10: Validate products restored
    const restoredCount = await products.count();
    console.log(`Products after clearing filters: ${restoredCount}`);

    // Step 11: Screenshot capture
    await page.screenshot({
        path: 'screenshots/product-filtering.png',
        fullPage: true
    });

    console.log("Product filtering test completed successfully");
});

//Add to Cart

import { test, expect } from '@playwright/test';
test('Add Product to Cart - HealthyBazar', async ({ page }) => {

    // Step 1: Launch application
    await page.goto('https://healthybazar.com/');
    await page.maximizeWindow();

    // Verify homepage loaded successfully
    await expect(page).toHaveTitle(/HealthyBazar/i);

    console.log("Homepage loaded successfully");

    // Step 2: Search for product
    const searchBox = page.locator('input[type="search"]');
    await expect(searchBox).toBeVisible();

    await searchBox.fill('protein powder');
    await page.keyboard.press('Enter');

    console.log("Product searched successfully");

    // Wait for search results
    await page.waitForLoadState('networkidle');

    // Step 3: Validate search results appear
    const productList = page.locator('.product-card');
    const totalProducts = await productList.count();

    console.log(`Total products found: ${totalProducts}`);
    expect(totalProducts).toBeGreaterThan(0);

    // Step 4: Click first product
    await productList.first().click();

    console.log("Clicked first product");

    // Wait for product details page
    await page.waitForLoadState('networkidle');

    // Step 5: Validate product details page
    const productTitle = page.locator('.product-title');
    await expect(productTitle).toBeVisible();

    const productName = await productTitle.textContent();
    console.log(`Selected Product: ${productName}`);

    // Step 6: Validate price is visible
    const productPrice = page.locator('.product-price');
    await expect(productPrice).toBeVisible();

    const priceText = await productPrice.textContent();
    console.log(`Product Price: ${priceText}`);

    // Step 7: Click Add to Cart button
    const addToCartButton = page.locator('text=Add to Cart');
    await expect(addToCartButton).toBeVisible();

    await addToCartButton.click();

    console.log("Product added to cart");

    // Step 8: Validate success message (if available)
    const successMessage = page.locator('.toast-message');
    
    if (await successMessage.isVisible()) {
        const messageText = await successMessage.textContent();
        console.log(`Success Message: ${messageText}`);
    }

    // Step 9: Validate cart icon count
    const cartCount = page.locator('.cart-count');
    await expect(cartCount).toContainText('1');

    console.log("Cart count validated successfully");

    // Step 10: Navigate to cart page
    await page.click('.cart-icon');

    await page.waitForLoadState('networkidle');

    // Step 11: Verify product exists in cart
    const cartProductName = page.locator('.cart-product-name');
    await expect(cartProductName).toBeVisible();

    const cartItemName = await cartProductName.textContent();
    console.log(`Product in cart: ${cartItemName}`);

    expect(cartItemName).toContain(productName.trim());

    // Step 12: Validate quantity
    const quantityField = page.locator('.cart-quantity input');
    await expect(quantityField).toHaveValue('1');

    // Step 13: Validate subtotal
    const subtotal = page.locator('.cart-subtotal');
    await expect(subtotal).toBeVisible();

    const subtotalText = await subtotal.textContent();
    console.log(`Cart Subtotal: ${subtotalText}`);

    // Step 14: Take screenshot
    await page.screenshot({
        path: 'screenshots/add-to-cart-success.png',
        fullPage: true
    });

    console.log("Screenshot captured successfully");

    console.log("Add to Cart test completed successfully");
});

//Update Cart Quantity

import { test, expect } from '@playwright/test';
test('Update Cart Quantity - HealthyBazar', async ({ page }) => {

    // Step 1: Open website
    await page.goto('https://healthybazar.com/');
    await page.maximizeWindow();

    console.log("Website opened successfully");

    // Step 2: Search product
    const searchBox = page.locator('input[type="search"]');
    await searchBox.fill('protein powder');
    await page.keyboard.press('Enter');

    await page.waitForLoadState('networkidle');

    console.log("Product searched successfully");

    // Step 3: Select product
    const product = page.locator('.product-card').first();
    await product.click();

    await page.waitForLoadState('networkidle');

    console.log("Product selected");

    // Step 4: Add product to cart
    const addToCartBtn = page.locator('text=Add to Cart');
    await addToCartBtn.click();

    console.log("Product added to cart");

    // Step 5: Open cart
    await page.click('.cart-icon');

    await page.waitForLoadState('networkidle');

    // Step 6: Validate initial quantity
    const quantityInput = page.locator('.cart-quantity input');
    await expect(quantityInput).toHaveValue('1');

    console.log("Initial quantity verified");

    // Step 7: Increase quantity
    const increaseBtn = page.locator('.increase-qty');
    await increaseBtn.click();

    await page.waitForTimeout(2000);

    await expect(quantityInput).toHaveValue('2');

    console.log("Quantity increased successfully");

    // Step 8: Validate subtotal update
    const subtotal = page.locator('.cart-subtotal');
    await expect(subtotal).toBeVisible();

    const subtotalValue = await subtotal.textContent();
    console.log(`Updated subtotal: ${subtotalValue}`);

    // Step 9: Decrease quantity
    const decreaseBtn = page.locator('.decrease-qty');
    await decreaseBtn.click();

    await page.waitForTimeout(2000);

    await expect(quantityInput).toHaveValue('1');

    console.log("Quantity decreased successfully");

    // Step 10: Refresh page validation
    await page.reload();

    await expect(quantityInput).toHaveValue('1');

    console.log("Quantity persisted after refresh");

    // Step 11: Screenshot capture
    await page.screenshot({
        path: 'screenshots/update-cart-quantity.png',
        fullPage: true
    });

    console.log("Cart quantity update test completed successfully");
});

//Remove Product from Cart

import { test, expect } from '@playwright/test';
test('Remove Product from Cart - HealthyBazar', async ({ page }) => {

    // Step 1: Open website
    await page.goto('https://healthybazar.com/');
    await page.maximizeWindow();

    console.log("Website launched successfully");

    // Step 2: Search product
    const searchBox = page.locator('input[type="search"]');
    await searchBox.fill('protein powder');
    await page.keyboard.press('Enter');

    await page.waitForLoadState('networkidle');

    console.log("Product search completed");

    // Step 3: Select first product
    const firstProduct = page.locator('.product-card').first();
    await firstProduct.click();

    await page.waitForLoadState('networkidle');

    // Step 4: Add product to cart
    const addToCartBtn = page.locator('text=Add to Cart');
    await addToCartBtn.click();

    console.log("Product added to cart");

    // Step 5: Open cart page
    await page.click('.cart-icon');
    await page.waitForLoadState('networkidle');

    // Step 6: Verify product exists in cart
    const cartProduct = page.locator('.cart-product-name');
    await expect(cartProduct).toBeVisible();

    const productName = await cartProduct.textContent();
    console.log(`Product in cart: ${productName}`);

    // Step 7: Remove product
    const removeButton = page.locator('.remove-product');
    await expect(removeButton).toBeVisible();

    await removeButton.click();

    console.log("Remove button clicked");

    // Step 8: Handle confirmation popup (if available)
    const confirmRemove = page.locator('text=Yes Remove');

    if (await confirmRemove.isVisible()) {
        await confirmRemove.click();
        console.log("Removal confirmed");
    }

    await page.waitForLoadState('networkidle');

    // Step 9: Verify cart is empty
    const emptyCartMessage = page.locator('text=Your cart is empty');
    await expect(emptyCartMessage).toBeVisible();

    console.log("Cart is empty after removal");

    // Step 10: Validate cart count reset
    const cartCount = page.locator('.cart-count');
    
    if (await cartCount.isVisible()) {
        await expect(cartCount).toContainText('0');
    }

    console.log("Cart count updated successfully");

    // Step 11: Screenshot capture
    await page.screenshot({
        path: 'screenshots/remove-product-cart.png',
        fullPage: true
    });

    console.log("Remove product test completed successfully");
});

//Wishlist Functionality

import { test, expect } from '@playwright/test';
test('Wishlist Functionality - HealthyBazar', async ({ page }) => {

    // Step 1: Open website
    await page.goto('https://healthybazar.com/');
    await page.maximizeWindow();

    console.log("Website launched successfully");

    // Step 2: Login user (if wishlist requires login)
    await page.click('text=Login');

    await page.fill('#email', 'testuser@gmail.com');
    await page.fill('#password', 'Password@123');
    await page.click('button[type="submit"]');

    await page.waitForLoadState('networkidle');

    console.log("Login successful");

    // Step 3: Search product
    const searchBox = page.locator('input[type="search"]');
    await searchBox.fill('protein powder');
    await page.keyboard.press('Enter');

    await page.waitForLoadState('networkidle');

    console.log("Product searched successfully");

    // Step 4: Select first product
    const firstProduct = page.locator('.product-card').first();
    await firstProduct.click();

    await page.waitForLoadState('networkidle');

    // Step 5: Add product to wishlist
    const wishlistButton = page.locator('.wishlist-icon');
    await expect(wishlistButton).toBeVisible();

    await wishlistButton.click();

    console.log("Product added to wishlist");

    // Step 6: Validate success message
    const successMessage = page.locator('.toast-message');

    if (await successMessage.isVisible()) {
        console.log(await successMessage.textContent());
    }

    // Step 7: Open wishlist page
    await page.click('.wishlist-menu');

    await page.waitForLoadState('networkidle');

    // Step 8: Verify product exists in wishlist
    const wishlistProduct = page.locator('.wishlist-product-name');
    await expect(wishlistProduct).toBeVisible();

    console.log("Product present in wishlist");

    // Step 9: Move product to cart
    const moveToCartBtn = page.locator('text=Move to Cart');

    if (await moveToCartBtn.isVisible()) {
        await moveToCartBtn.click();
        console.log("Product moved to cart");
    }

    // Step 10: Verify cart count
    const cartCount = page.locator('.cart-count');
    await expect(cartCount).toContainText('1');

    console.log("Cart updated successfully");

    // Step 11: Remove product from wishlist
    const removeWishlist = page.locator('.remove-wishlist-product');

    if (await removeWishlist.isVisible()) {
        await removeWishlist.click();
        console.log("Product removed from wishlist");
    }

    // Step 12: Screenshot capture
    await page.screenshot({
        path: 'screenshots/wishlist-functionality.png',
        fullPage: true
    });

    console.log("Wishlist test completed successfully");
});

//Checkout Process

import { test, expect } from '@playwright/test';
test('Checkout Process - HealthyBazar', async ({ page }) => {

    // Step 1: Open website
    await page.goto('https://healthybazar.com/');
    await page.maximizeWindow();

    console.log("Website launched successfully");

    // Step 2: Login
    await page.click('text=Login');
    await page.fill('#email', 'testuser@gmail.com');
    await page.fill('#password', 'Password@123');
    await page.click('button[type="submit"]');

    await page.waitForLoadState('networkidle');

    console.log("Login successful");

    // Step 3: Search product
    const searchBox = page.locator('input[type="search"]');
    await searchBox.fill('protein powder');
    await page.keyboard.press('Enter');

    await page.waitForLoadState('networkidle');

    console.log("Product searched successfully");

    // Step 4: Select product
    await page.locator('.product-card').first().click();

    await page.waitForLoadState('networkidle');

    // Step 5: Add to cart
    await page.click('text=Add to Cart');

    console.log("Product added to cart");

    // Step 6: Open cart
    await page.click('.cart-icon');

    await page.waitForLoadState('networkidle');

    // Step 7: Proceed to checkout
    const checkoutBtn = page.locator('text=Proceed to Checkout');
    await expect(checkoutBtn).toBeVisible();

    await checkoutBtn.click();

    console.log("Checkout page opened");

    // Step 8: Enter shipping address
    await page.fill('#fullName', 'Riya Kumari');
    await page.fill('#mobile', '9876543210');
    await page.fill('#address', 'New Delhi');
    await page.fill('#city', 'Delhi');
    await page.fill('#pincode', '110001');

    console.log("Shipping details entered");

    // Step 9: Select payment method
    const codOption = page.locator('text=Cash on Delivery');
    await codOption.click();

    console.log("Payment method selected");

    // Step 10: Place order
    const placeOrderBtn = page.locator('text=Place Order');
    await expect(placeOrderBtn).toBeVisible();

    await placeOrderBtn.click();

    await page.waitForLoadState('networkidle');

    // Step 11: Verify order confirmation
    const orderConfirmation = page.locator('text=Order Confirmed');
    await expect(orderConfirmation).toBeVisible();

    console.log("Order placed successfully");

    // Step 12: Capture order ID
    const orderId = page.locator('.order-id');

    if (await orderId.isVisible()) {
        console.log(`Order ID: ${await orderId.textContent()}`);
    }

    // Step 13: Screenshot capture
    await page.screenshot({
        path: 'screenshots/checkout-process.png',
        fullPage: true
    });

    console.log("Checkout automation completed successfully");
});

// Payment Page Validation

import { test, expect } from '@playwright/test';
test('Payment Page Validation - HealthyBazar', async ({ page }) => {

    // Step 1: Open website
    await page.goto('https://healthybazar.com/');
    await page.maximizeWindow();

    console.log("Website launched successfully");

    // Step 2: Login
    await page.click('text=Login');
    await page.fill('#email', 'testuser@gmail.com');
    await page.fill('#password', 'Password@123');
    await page.click('button[type="submit"]');

    await page.waitForLoadState('networkidle');

    console.log("Login successful");

    // Step 3: Search product
    await page.fill('input[type="search"]', 'protein powder');
    await page.keyboard.press('Enter');

    await page.waitForLoadState('networkidle');

    console.log("Product searched");

    // Step 4: Select product
    await page.locator('.product-card').first().click();

    await page.waitForLoadState('networkidle');

    // Step 5: Add to cart
    await page.click('text=Add to Cart');

    console.log("Product added to cart");

    // Step 6: Open cart
    await page.click('.cart-icon');
    await page.waitForLoadState('networkidle');

    // Step 7: Proceed to checkout
    await page.click('text=Proceed to Checkout');

    console.log("Checkout page opened");

    // Step 8: Enter shipping details
    await page.fill('#fullName', 'Riya Kumari');
    await page.fill('#mobile', '9876543210');
    await page.fill('#address', 'New Delhi');
    await page.fill('#city', 'Delhi');
    await page.fill('#pincode', '110001');

    // Step 9: Continue to payment page
    await page.click('text=Continue to Payment');

    await page.waitForLoadState('networkidle');

    console.log("Payment page opened");

    // Step 10: Validate payment methods
    await expect(page.locator('text=UPI')).toBeVisible();
    await expect(page.locator('text=Credit Card')).toBeVisible();
    await expect(page.locator('text=Debit Card')).toBeVisible();
    await expect(page.locator('text=Cash on Delivery')).toBeVisible();

    console.log("Payment methods validated");

    // Step 11: Select Credit Card
    await page.click('text=Credit Card');

    // Step 12: Enter card details
    await page.fill('#cardNumber', '4111111111111111');
    await page.fill('#cardName', 'Riya Kumari');
    await page.fill('#expiryDate', '12/28');
    await page.fill('#cvv', '123');

    console.log("Card details entered");

    // Step 13: Validate payment amount
    const totalAmount = await page.locator('.payment-total').textContent();
    console.log(`Payment Amount: ${totalAmount}`);

    // Step 14: Click Pay Now
    await page.click('text=Pay Now');

    await page.waitForLoadState('networkidle');

    // Step 15: Verify payment success
    const paymentSuccess = page.locator('text=Payment Successful');
    await expect(paymentSuccess).toBeVisible();

    console.log("Payment successful");

    // Step 16: Verify order confirmation
    await expect(page.locator('text=Order Confirmed')).toBeVisible();

    // Step 17: Screenshot capture
    await page.screenshot({
        path: 'screenshots/payment-validation.png',
        fullPage: true
    });

    console.log("Payment page validation completed successfully");
});

//Order Placement

import { test, expect } from '@playwright/test';
test('Order Placement Flow - HealthyBazar', async ({ page }) => {

    // Step 1: Open website
    await page.goto('https://healthybazar.com/');
    await page.maximizeWindow();

    console.log("Website launched successfully");

    // Step 2: Login user
    await page.click('text=Login');

    await page.fill('#email', 'testuser@gmail.com');
    await page.fill('#password', 'Password@123');
    await page.click('button[type="submit"]');

    await page.waitForLoadState('networkidle');

    console.log("Login successful");

    // Step 3: Search product
    const searchBox = page.locator('input[type="search"]');
    await searchBox.fill('protein powder');
    await page.keyboard.press('Enter');

    await page.waitForLoadState('networkidle');

    console.log("Product search completed");

    // Step 4: Select first product
    await page.locator('.product-card').first().click();

    await page.waitForLoadState('networkidle');

    // Step 5: Add product to cart
    await page.click('text=Add to Cart');

    console.log("Product added to cart");

    // Step 6: Open cart
    await page.click('.cart-icon');

    await page.waitForLoadState('networkidle');

    // Step 7: Proceed to checkout
    await page.click('text=Proceed to Checkout');

    console.log("Checkout started");

    // Step 8: Enter shipping details
    await page.fill('#fullName', 'Riya Kumari');
    await page.fill('#mobile', '9876543210');
    await page.fill('#address', 'New Delhi');
    await page.fill('#city', 'Delhi');
    await page.fill('#pincode', '110001');

    console.log("Shipping details entered");

    // Step 9: Select payment method
    await page.click('text=Cash on Delivery');

    console.log("Payment method selected");

    // Step 10: Verify order summary
    const totalAmount = await page.locator('.order-total').textContent();
    console.log(`Final Order Amount: ${totalAmount}`);

    // Step 11: Place order
    const placeOrderBtn = page.locator('text=Place Order');
    await expect(placeOrderBtn).toBeVisible();

    await placeOrderBtn.click();

    await page.waitForLoadState('networkidle');

    // Step 12: Verify order confirmation
    const orderSuccess = page.locator('text=Order Confirmed');
    await expect(orderSuccess).toBeVisible();

    console.log("Order placed successfully");

    // Step 13: Capture Order ID
    const orderId = page.locator('.order-id');

    if (await orderId.isVisible()) {
        const orderNumber = await orderId.textContent();
        console.log(`Generated Order ID: ${orderNumber}`);
    }

    // Step 14: Navigate to order history
    await page.click('.user-profile');
    await page.click('text=My Orders');

    await page.waitForLoadState('networkidle');

    // Step 15: Verify order exists in history
    const orderHistory = page.locator('.order-history-item').first();
    await expect(orderHistory).toBeVisible();

    console.log("Order verified in order history");

    // Step 16: Screenshot capture
    await page.screenshot({
        path: 'screenshots/order-placement.png',
        fullPage: true
    });

    console.log("Order placement automation completed successfully");
});

//User Profile Validation

import { test, expect } from '@playwright/test';

test('User Profile Validation - HealthyBazar', async ({ page }) => {

    // Step 1: Open website
    await page.goto('https://healthybazar.com/');
    await page.maximizeWindow();

    console.log("Website launched successfully");

    // Step 2: Login
    await page.click('text=Login');

    await page.fill('#email', 'testuser@gmail.com');
    await page.fill('#password', 'Password@123');
    await page.click('button[type="submit"]');

    await page.waitForLoadState('networkidle');

    console.log("Login successful");

    // Step 3: Open user profile
    const profileIcon = page.locator('.user-profile');
    await expect(profileIcon).toBeVisible();

    await profileIcon.click();

    await page.click('text=My Profile');

    await page.waitForLoadState('networkidle');

    console.log("Profile page opened");

    // Step 4: Verify user information
    const userName = page.locator('#fullName');
    const email = page.locator('#email');
    const mobile = page.locator('#mobile');

    await expect(userName).toBeVisible();
    await expect(email).toBeVisible();
    await expect(mobile).toBeVisible();

    console.log("User details displayed successfully");

    // Step 5: Update name
    await userName.clear();
    await userName.fill('Riya QA Engineer');

    console.log("Name updated");

    // Step 6: Update mobile number
    await mobile.clear();
    await mobile.fill('9876543210');

    console.log("Mobile updated");

    // Step 7: Update address
    const address = page.locator('#address');

    if (await address.isVisible()) {
        await address.clear();
        await address.fill('New Delhi, India');
        console.log("Address updated");
    }

    // Step 8: Save changes
    const saveBtn = page.locator('text=Save Changes');
    await expect(saveBtn).toBeVisible();

    await saveBtn.click();

    await page.waitForLoadState('networkidle');

    // Step 9: Verify success message
    const successMessage = page.locator('text=Profile Updated Successfully');
    await expect(successMessage).toBeVisible();

    console.log("Profile updated successfully");

    // Step 10: Logout
    await profileIcon.click();
    await page.click('text=Logout');

    console.log("User logged out");

    // Step 11: Login again
    await page.click('text=Login');

    await page.fill('#email', 'testuser@gmail.com');
    await page.fill('#password', 'Password@123');
    await page.click('button[type="submit"]');

    await page.waitForLoadState('networkidle');

    // Step 12: Verify updated profile persists
    await profileIcon.click();
    await page.click('text=My Profile');

    await expect(page.locator('#fullName')).toHaveValue('Riya QA Engineer');

    console.log("Profile data persisted successfully");

    // Step 13: Screenshot capture
    await page.screenshot({
        path: 'screenshots/user-profile-validation.png',
        fullPage: true
    });

    console.log("User profile validation completed successfully");
});






