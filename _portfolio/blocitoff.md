---
layout: post
title: Knock Out
thumbnail-path: "img/blocitoff.png"
short-description: Knock Out is an application that allows users to create self-destructing to-do lists.

---

{:.center}
![]({{ site.baseurl }}/img/blocitoff.png)

## Summary:

Knock Out is an application that allows users to create self-destructing to-do lists. It is built using Ruby on Rails

## Explanation

Knock Out aims to keep to-do lists manageable by automatically deleting to-do items that have not been completed after seven days. The hypothesis is that if the to-do item is not important enough to be completed in seven days, it doesn't belong on your to-do list.

## Problem:

The tasks for this application were:

* As a user, I want to sign up for a free account by providing a user name, password and email
* As a user, I want to sign in and out of Blocitoff
* As a user, I want to see my profile page
* As a user, I want to create multiple to-do items
* As a developer, I want to seed the development database automatically with users and to-do items
* As a user, I want to mark to-do items as complete and have them deleted
* As a user, I want to see how old a to-do item is
* As a user, my to-dos should be automatically deleted seven days after their creation date

## Solution:

To create the ability to have multiple to-do items I created an items controller and model. After that I built partials to add new items.

Controller:

{% highlight ruby %}

class ItemsController < ApplicationController
  def new
    @item = Item.new
  end

  def create
    @item = Item.new(item_params)
    @item.user = current_user

    if @item.save
      flash[:notice] = "Item was saved."
      redirect_to current_user
    else
      flash[:error] = "There was an error saving the item. Please try again."
      render :new
    end
  end

  def destroy
      @item = current_user.items.find(params[:id])

      if @item.destroy
        flash[:notice] = "Item completed!"
      else
        flash[:alert] = "There was an error deleting the item, please try again."
      end

      respond_to do |format|
        format.html
        format.js
      end

    end

  private

  def item_params
    params.require(:item).permit(:name, :user)
  end
end

{% endhighlight %}

Partial:

{% highlight ruby %}

<% @items.each do |item| %>
  <div class="media" id="item-<%= item.id %>">
    <div class="media-body">
      <h4 class="media-heading">
        <%= item.name %>
        <% if current_user == @user %>
          : <%= link_to '', [item.user, item], method: :delete, remote: true, class: 'glyphicon glyphicon-ok' %>
        <% end %>
      </h4>
      <small>
        <%= distance_of_time_in_words(Time.now, item.created_at.to_date + 7 ) %> left <br>
      </small>
    </div>
  </div>
<% end %>

{% endhighlight %}

To delete a task manually I implemented a delete link in my items partial shown above.

{% highlight ruby %}

<%= link_to "", item, method: :delete, class: 'glyphicon glyphicon-ok' %>

{% endhighlight %}

To implement automatic deletion after 7 days I built a custom rake task in my application to execute it.

{% highlight ruby %}

namespace :todo do
  desc "Delete items older than seven days"
  task delete_items: :environment do
    Item.where("created_at <= ?", Time.now - 7.days).destroy_all
  end

end

{% endhighlight %}

## Conclusion:

Knock Out was another back-end specialization project at Bloc. This was a fun project to work on because it allowed me to learn how to implement a custom rake task and further use skills in Ruby and Rails to create a to-do list for users to use.
