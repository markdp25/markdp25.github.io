---
layout: post
title: Eventrics
thumbnail-path: "img/eventrics.png"
short-description: Eventrics is an analytics service to track events on websites.

---

{:.center}
![]({{ site.baseurl }}/img/eventrics.png)

## Summary:

Eventrics is an analytics service to track events on websites built using Ruby on Rails.

## Explanation

Eventrics provides a few key features:

* A client-side JavaScript snippet that allows a user to track events on their website.
* A server-side API that captures and saves those events to a database.
* A Rails application that displays the captured event data for a user.


## Problem:

The tasks we were given to build this application were:

* As a user, I want to sign up for a free account by providing a name, password, and email
* As a user, I want to sign in and out of Blocmetrics
* As a user, I want to register an application with Blocmetrics for tracking
* As a user, I want events associated with registered applications
* As a developer, I want to receive incoming events in an API controller
* As a user, I want to use JavaScript to capture client-side events in my application
* As a user, I want to see a graph of events for each registered application

## Solution:

To register an application we built a controller for the Registered applications and then implemented the views. Here is the new application view

{% highlight ruby %}

<h3>Add an application</h3>
<%= form_for(@app) do |f| %>
  <%= f.label :name %>
  <%= f.text_field :name, class: 'form-control' %>

  <label for="registered_application_URL">URL</label>
  <%= f.text_field :url, class: 'form-control' %>

  <%= f.submit "Register an application", class: "btn btn-primary" %>
<% end %>

{% endhighlight %}

To associate events with the applications, I created an Event model that belongs to a registered application. After doing that I changed the registered show page in the Registered Application Controller by doing

{% highlight ruby %}

def show
      @app = RegisteredApplication.find(params[:id])
      @events = @app.events.group_by(&:name)
end

{% endhighlight %}

To receive events from the registered applications I created an API controller and used CORS to help with the AJAX request for the events that will be sent to the API, which helps store the event.

{% highlight ruby %}

class API::EventsController < ApplicationController

   before_action :set_access_control_headers

   def set_access_control_headers
     headers['Access-Control-Allow-Origin'] = '*'
     headers['Access-Control-Allow-Methods'] = 'POST, GET, OPTIONS'
     headers['Access-Control-Allow-Headers'] = 'Content-Type'
   end

    skip_before_action :verify_authenticity_token

   def create
     registered_application = RegisteredApplication.find_by(url: request.env['HTTP_ORIGIN'])
     @event = Event.new(event_params)
     @event.registered_application = registered_application

      if registered_application.nil?
        render json: "Unregistered application", status: :unprocessable_entity
      elsif @event.save
        render json: @event, status: :created
      else
        render json: {errors: @event.errors}, status: :unprocessable_entity
      end
   end

   def preflight
    head 200
  end
{% endhighlight %}

To track events I implemented JavaScript snippets in another application I built.

{% highlight js %}

var blocmetrics = {};
  blocmetrics.report = function(eventName){
    var event = {event: { name: eventName }};

    var request = new XMLHttpRequest();
    request.open("POST", "http://localhost:3000/api/events", true);
    request.setRequestHeader('Content-Type', 'application/json');
    request.send(JSON.stringify(event));
  };

$(document).on('turbolinks:load', function(){
  $('.user-sign-up').click(function(){
    blocmetrics.report('user clicked sign up link');
  });
});

{% endhighlight %}

To show charts on the page of the events I used the JavaScript charting library Chartkick which generated a pie chart

{% highlight ruby %}

<%= pie_chart @registered_application.events.group(:name).count %>

{% endhighlight %}

And I used the Groupdate gem to show a line chart and used PostgresSQL to support it.

{% highlight ruby %}

<%= line_chart @registered_application.events.group_by_day(:created_at).count %>

{% endhighlight %}

## Conclusion:

The project was a back-end specialization project for Bloc. It was challenging and fun to be able to build an application that utilized different gems that I was not used to working with as well as implementing JavaScript to be able to track events.
