---
layout: post
title: Bloccit
thumbnail-path: "img/bloccit.png"
short-description: Bloccit is a Reddit replica

---

{:.center}
![]({{ site.baseurl }}/img/bloccit.png)

## Summary:

Knock Out is a replica of Reddit where people can post, vote on, and comment. It was my first time building an app using Rails.

## Explanation

A Reddit replica to teach the fundamentals of web development, Ruby and Rails. Knock Out was the first web application that I built.

## Problem:

Building a fully functional Rails app that allows users to post, vote, and comment on posts. It also uses authorization for users and relationships between users, posts, and comments.

## Solution:

Here is a look at the post controller:

{% highlight ruby %}

class PostsController < ApplicationController
   before_action :require_sign_in, except: :show
   before_action :authorize_user, except: [:show, :new, :create]

  def show
    @topic = Topic.find(params[:topic_id])
    @post = Post.find(params[:id])
  end

  def new
    @topic = Topic.find(params[:topic_id])
    @post = Post.new
  end

  def create
     @topic = Topic.find(params[:topic_id])
     @post = @topic.posts.build(post_params)
     @post.user = current_user


     if @post.save
       flash[:notice] = "Post was saved."
       redirect_to [@topic, @post]
{% endhighlight %}

Here is the post model that shows its validations, associations, and voting:

{% highlight ruby %}
class Post < ApplicationRecord
  belongs_to :topic
  belongs_to :user
  has_many :comments, dependent: :destroy
  has_many :votes, dependent: :destroy
  has_many :favorites, dependent: :destroy

  default_scope { order('rank DESC') }
  scope :visible_to, -> (user) { user ? all : joins(:topic).where('topics.public' => true) }

  validates :title, length: { minimum: 5 }, presence: true
  validates :body, length: { minimum: 20 }, presence: true
  validates :topic, presence: true
  validates :user, presence: true

  def up_votes
     votes.where(value: 1).count
   end

   def down_votes
     votes.where(value: -1).count
   end
  {% endhighlight %}

  Here is a look at a post view that shows the use of partials:

  {% highlight ruby %}

  <h1>Edit Post</h1>

 <div class="row">
   <div class="col-md-4">
     <p>Guidelines for posts</p>
     <ul>
       <li>Make sure it rhymes.</li>
       <li>Don't use the letter "A".</li>
       <li>The incessant use of hashtags will get you banned.</li>
     </ul>
   </div>
 <div class="col-md-8">
   <%= render partial: 'form', locals: { topic: @post.topic, post: @post } %>
   </div>
 </div>

 {% endhighlight %}

## Conclusion:

This project was one of the first I built using Ruby and Rails so it was challenging and fun to be able to learn about different features of Rails while being able to implement them right away
