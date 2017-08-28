---
layout: post
title: BlocJams Angular
thumbnail-path: "img/bloc_jams_bg.jpg"
short-description: BlocJams is a music player web app refactored to AngularJS.

---

{:.center}
![]({{ site.baseurl }}/img/bloc_jams_bg.jpg)

## Summary:

 In this project, I took Foundation Bloc Jams and refactored it using AngularJS, to make it a more dynamic and sophisticated front-end application.

## Explanation

The purpose of Bloc Jams Angular was to create a digital music player from scratch, using AngularJS, where the user could easily navigate through the collection of albums and choose songs to play in the song player. The song player needed to be fully functional with “play”, “pause”, “previous”, and “next” buttons, along with rewind, fast-forward, and volume controls

## Problem:

This project was intended to learn and implement the beginnings of my Angular front-end development skills at Bloc.

The problems we had to solve were:

1. As a developer, I want to bootstrap Angular to my application.
2. As a developer, I want to configure routing and states for my application.
3. As a developer, I want to implement controllers for my application’s views.
4. As a developer, I want to create a service that handles song playback.
5. As a developer, I want to write a directive that controls song and volume sliders.
6. As a developer, I want to add a time code filter to display time properly.

## Solution:

First, I had to learn what Angular was and how the framework worked.

Second, I configured Angular to Bloc Jams, I migrated files and code from Bloc Jams to Angular Bloc Jams and then added the Angular script source, declared an Angular Module, and bootstrapped Angular to start the application.

{{% highlight js %}}


    function config($stateProvider, $locationProvider){
          $locationProvider
              .html5Mode ({
                  enabled: true,
                  requireBase: false
              });

          $stateProvider
              .state('landing', {
                  url: '/',
                  controller: 'LandingCtrl as landing',
                  templateUrl: '/templates/landing.html'
              })

  {{% endhighlight %}}

Third, I created templates and configured UI-Routing, which included location and state providers, in order to display the templates in the view. Then, I learned more about Angular directives and applied them to the HTML documents - adding animation and functionality to the web site.


{{% highlight js %}}

        <div class="total-time">{{ playerBar.songPlayer.currentSong.duration | timecode }}</div>
    </div>

    <div class="control-group volume">
    <a class='icon' ng-click='playerBar.songPlayer.toggleMute()'>
        <span class='ion-volume-high'
              ng-show='!playerBar.songPlayer.currentSong.muted'>
        </span>
        <span class='ion-volume-mute'
              ng-show='playerBar.songPlayer.currentSong.muted'>
        </span>
    </a>
    <seek-bar value="{{ playerBar.songPlayer.volume }}" max=100 on-change="playerBar.songPlayer.setVolume(value)"></seek-bar>


{{% endhighlight %}}


Fourth, controllers were introduced as a means to control the flow of data in Angular applications to help keep the code separate and organized in their modules. I learned how to define a controller, when to use one, along with the inner workings of a functioning controller. In this section, I created the necessary controllers for Bloc Jams Angular.

{{% highlight js %}}

     function PlayerBarCtrl(Fixtures, SongPlayer) {
         this.albumData = Fixtures.getAlbum();
         this.songPlayer = SongPlayer;
     }

     angular
         .module('blocJams')
         .controller('PlayerBarCtrl', ['Fixtures', 'SongPlayer', PlayerBarCtrl]);
 })();

 {{% endhighlight %}}

Finally, Angular Services was the last detail of my project. This was important  because unlike controllers, services are objects that can share data and behavior across several components (controllers, directives, filters, even other services) throughout an application. This is crucial for the functionality of an Angular application.


## Conclusion:

Although Bloc Jams Angular was a tough project for me, the learnings and challenge expanded my knowledge and boosted my confidence as a programmer. This version of Bloc Jams worked successfully and I was able to add not only autoplay in so that when one song ends, the next song automatically plays, but also a mute option using toggle.mute so that when a person clicks on the volume icon the song can be muted.
