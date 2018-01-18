---
layout: post
title: Blogipedia
thumbnail-path: "img/Blogipedia.png"
short-description: Blogipedia is a Wikipedia clone built using Ruby on Rails.

---

{:.center}
![]({{ site.baseurl }}/img/Blogipedia.png)

## Summary:

Blogipedia is a Wikipedia clone built using Ruby on Rails.

## Explanation

Blogipedia was one of the first applications I built using Ruby on Rails. In this application we wanted to accomplish numerous things:

* As a user, I want to sign up for a free account by providing a user name, password and email
* As a user, I want to sign in and out of Blocipedia
* As a user with a standard account, I want to create, read, update, and delete public wikis
* As a developer, I want to offer three user roles: admin, standard, or premium
* As a developer, I want to seed the development database automatically with users and wikis
* As a user, I want to upgrade my account from a free to a paid plan
* As a premium user, I want to create private wikis
* As a user, I want to edit wikis using Markdown syntax
* As a premium user, I want to add and remove collaborators for my private wikis

## Problem:

This project was intended to learn and implement the foundations learned in Ruby and Rails to build a wiki clone with different user roles, ability to upgrade your account, use markdown, and collaborate on wikis.

## Solution:

To create different user roles I set the default user role to standard in the User model and used enum to set the other roles.

{% highlight ruby %}

before_save { self.email = email.downcase }
  before_save {self.role ||= :standard }

  enum role: [:standard, :premium, :admin]
  after_initialize :set_standard, :if => :new_record?

  def set_standard
    self.role ||= :standard
  end

{% endhighlight %}

To upgrade the account we used the gem Stripe to allow users to upgrade. This was all done in a Charges Controller.

{% highlight ruby %}

class ChargesController < ApplicationController
  def new
    @stripe_btn_data = {
      key: "#{ Rails.configuration.stripe[:publishable_key] }",
      description: "Blocipedia Membership - #{current_user.email}",
      amount: @amount
    }
  end

  def create
    @amount = 1500

    customer = Stripe::Customer.create(
      :email => params[:stripeEmail],
      :source  => params[:stripeToken]
    )

    charge = Stripe::Charge.create(
      :customer    => customer.id,
      :amount      => @amount,
      :description => 'Rails Stripe customer',
      :currency    => 'usd'
    )

    flash[:success] = "Thanks for upgrading, #{current_user.email}!"
    current_user.update_attribute(:role, 'premium')
    redirect_to edit_user_registration_path

    rescue Stripe::CardError => e
    flash[:error] = e.message
    redirect_to new_charge_path
  end
end

{% endhighlight %}

Users were also allowed to downgrade from their account.

We made the wikis use markdown. To do that we used the Redcarpet gem and then implementing it in the Wiki views like

{% highlight ruby %}

<h1><%= markdown(@wiki.title) %></h1>
<div class="row">
  <div class="col-md-8">
    <p><%= markdown(@wiki.body) %></p>
  </div>

{% endhighlight %}

To allow users to collaborate on wikis we made it possible in the view by searching for the email address of the collaborator.

{% highlight ruby %}

<% if current_user.premium? || current_user.admin? %>
  <div class="col-md-8">
    <h3>Add Collaborators</h3>
    <%= form_tag collaborations_path do |f| %>
      <%= hidden_field_tag :wiki_id, wiki.id %>
      Add Collaborators using their email address
      <%= text_field_tag :email%>  <br>

      <%= submit_tag "Save Email" %>
    <% end %>
  </div>

  {% endhighlight %}

## Conclusion:

I enjoyed working on this project and being able to implement numerous different features on Rails by having collaborators, the ability to upgrade or downgrade your account, and using markdown syntax. I enjoyed being able to learn more about different gems in Ruby and being able to work with them hands on.
