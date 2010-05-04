# Minimum Recurring Payments

## What's This All About?

These are my thoughts, as I get my head wrapped around how to handle recurring payments in web apps, using Rails and Active Merchant to send customer data over to Braintree.

At the moment, these will just be loosely-gathered thoughts. I might add some code in at some point, but we'll see. I might also just use Freemium or another library built for this general purpose. I'd really like it to be as simple as possible, though. Just enough to work.

## Some Groundwork

I'm using a lot of the thinking of Freemium, but I expect to either build this on top of Active Merchant, or to use a tweaked version of Freemium.

When the cron job runs, the script checks the database for all subscriptions that have a "paid_through" date less than or equal to Date.today and a subscription_plan_id that is not "0" (closed account).


## Database Schema

### Subscription
    t.integer   user_id
    t.integer   subscription_plan_id