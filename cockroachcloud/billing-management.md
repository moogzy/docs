---
title: Billing Management
summary: Manage billing for your organization
toc: true
---

The **Billing** page contains an overview of your charges and the payment details on file for your {{ site.data.products.db }} organization. To view the **Billing** page, [log in](https://cockroachlabs.cloud/) and click **Billing**.

[Console Admins](console-access-management.html#console-admin) can set up and manage billing for the organization.

{{site.data.alerts.callout_info}}
Setting up billing information for your organization allows you to use [cloud storage for bulk operations](run-bulk-operations.html). Organizations without billing information are limited to [using `userfile` storage for bulk operations](run-bulk-operations.html).
{{site.data.alerts.end}}

## Set up billing for an organization

1. On the **Billing** page, select the **Payment details** tab.
1. Click **Add a credit card** in the **Payment method** field.
1. In the **Edit payment method** dialog, enter the credit or debit card details.
1. Click **Save card**.
1. Click **Add a billing email** in the **Billing contact info** section.
1. In the **Edit billing email*** the email address at which you want to get invoices for the organization.
1. Click **Submit**.
1. Click **Add a billing address** in the **Billing contact info** section.
1. Enter the address associated with your payment method. This address appears on your monthly invoice and should be the legal address of your home or business.
1. Click **Submit**.

## Change the billing email address

1. On the **Billing** page, select the **Payment details** tab.
1. Click the pencil icon for the **Email** field under **Billing contact info**.
1. Enter the new email address at which you want get invoices for the organization.
1. Click **Submit**.

## Change the payment method

1. On the **Billing** page, select the **Payment details** tab.
1. Click the pencil icon for the **Payment method** field.
1. In the **Edit payment method** dialog, enter the credit or debit card details.
1. Click **Save card**.
1. In the **Billing contact info** section, click the pencil icon for the **Billing address** field.
1. Enter the address associated with your new payment method. This address appears on your monthly invoice and should be the legal address of your home or business.
1. Click **Submit**.

## Delete your credit card information

We keep a card on file after the associated organization is deleted so we can process pending charges. You can [contact Support](https://support.cockroachlabs.com) to remove your card once your charges are settled.

## Check trial code details

If you used a {{ site.data.products.dedicated }} trial code while [creating a cluster](create-your-cluster.html#step-8-enter-billing-details), you can check the trial expiration details on the **Overview** tab of the **Billing** page.

{{site.data.alerts.callout_info}}
Your credit card will be charged after the trial ends.
{{site.data.alerts.end}}

{% comment %}
## View credits balance

If your organization has an annual contract with {{ site.data.products.db }}, the **Overview** tab of the **Billing** page will display your contract amount and the renewal date. Under the **Spend** section, you can also see how many credits your organization has used out of your total contract amount.

## View invoices

You can view all of your organization's past invoices on the **Invoices** tab of the **Billing** page. Download any invoice to see the details of your charges for a billing period.
{% endcomment %}
