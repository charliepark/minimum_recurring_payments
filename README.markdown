# Minimum Recurring Payments

## What's This All About?

These are my thoughts, as I get my head wrapped around how to handle recurring payments in web apps, using Rails and Active Merchant to send customer data over to Braintree.

At the moment, these will just be loosely-gathered thoughts. I might add some code in at some point, but we'll see. I might also just use Freemium or another library built for this general purpose. I'd really like it to be as simple as possible, though. Just enough to work.

## Some Groundwork

-I'm using a lot of the thinking of Freemium, but I expect to either build this on top of Active Merchant, or to use a tweaked version of Freemium.-

Because of how much work has gone into Active Merchant, especially by the Braintree team, it makes sense to use that as the plumbing. We'll still need a cron job and homegrown scripts to decide when to bill, and how much, and so on.

## Database Schema

### Subscription
    t.integer   user_id
    t.integer   subscription_plan_id
    t.date      paid_through
    t.date      expire_on
    t.integer   braintree_customer_id
    t.date      annual_billing_reminder_sent_on
    t.boolean   send_close_down_email
    t.datetime  deleted_at

### Subscription Plan
    t.string    name
    t.integer   rate
    t.boolean   yearly    # this is necessary, as it sets the date for the "paid_through" value


One of the Subscription Plans should be "closed account". With this plan, the user can still access her account, but will not be billed again. At the paid_through date on her Subscription, if her Subscription has a "send_close_down_email" value of 1, she should be sent a "thank you for using Monotask" e-mail that confirms that the subscription is terminated; also, the Subscription's "deleted_at" should be updated with the current datetime.


## How Does the Recurring Charge Work?

When the cron job runs, the script checks the database for all subscriptions that have a "paid_through" date less than or equal to Date.today and a subscription_plan_id that is not "0" (closed account).

For each subscription, the script sends a billing request to Braintree, with the @subscription.subscription_plan.rate as the amount to charge.

If the charge comes back with a success code, the script then updates the @subscription.paid_through ... if @subscription.subscription_plan.yearly is true, it updates the paid_through to @subscription.paid_through + 1.year. Else, it updates the paid_through to @subscription.paid_through + 1.month.






## Some Notes on Railscast #144 (Active Merchant Basics)

* Ryan is using PayPal as his gateway, but we'll be using Braintree.

    gem install activemerchant

* Note: I'm using RVM, so I don't use sudo on my gems

    # purchase.rb
    require "rubygems"
    require "active_merchant"

    ActiveMerchant

    gateway = ActiveMerchant::Billing::BraintreeGateway.new(
      :login     => "",
      :password  => "",
      :signature => "",
      )

    credit_card = ActiveMerchant::Billing::CreditCard.new(
      
      )

















