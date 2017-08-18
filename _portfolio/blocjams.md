---
layout: post
title: BlocJams
thumbnail-path: "img/bloc_jams_screenshot.png"
short-description: BlocJams is a music player web app built with JavaScript and jQuery.
---

{:.center}
![]({{site.baseurl}}/img/bloc_jams_screenshot.png)

## Summary:

BlocJams is a music streaming web application like Spotify that used HTML, CSS, JavaScript, and jQuery to build.

## Explanation:

BlocJams was the first project built during my web development studies at Bloc. During this project we had integrate several things: responsive design, animations, and JavaScript to create a functioning collection of albums pages along with the ability to play a sample album. We also used DOM scripting done originally by vanilla JavaScript but was later refactored using jQuery.

## Problem:

This project was intended to learn and implement the beginnings of my front-end development skills at Bloc.

The problems we had to solve were:

* One of the issues to solve for the Bloc-Jams project was to ensure that it was responsive to different screen sizes and devices.

* Another issue was how to program easier as opposed to JavaScript.

* How to make Bloc-Jams more dynamic.

## Solution:

To make our page responsive we used viewport and media queries

{% highlight js %}

@media (max-width: 640px) and (min-width: 320px) {
     .album-view-details {
         text-align: center;
     }

     .album-view-title {
         margin-top: 0;
     }
 }

 @media (max-width: 1024px) and (min-width: 320px) {
    .album-view-song-list {
         position: relative;
         top: 1rem;
         width: 80%;
         margin: auto;
     }
{% endhighlight %}

We used jQuery to help program easier with JavaScript and to make our page more dynamic.

{% highlight js %}

var createSongRow = function(songNumber, songName, songLength) {
    var template =
        '<tr class="album-view-song-item">'
    + '     <td class="song-item-number" data-song-number="' + songNumber + '">' + songNumber + '</td>'
    + '     <td class="song-item-title">' + songName + '</td>'
    + '     <td class="song-item-duration">' + filterTimeCode(songLength) + '</td>'
    + '     </tr>'
    ;

    var $row = $(template);

    var clickHandler = function() {

        var songNumber = parseInt($(this).attr('data-song-number'));

        if (currentlyPlayingSongNumber !== null) {

            var currentlyPlayingCell = getSongNumberCell(currentlyPlayingSongNumber);

            currentlyPlayingCell = getSongNumberCell(currentlyPlayingSongNumber);
            currentlyPlayingCell.html(currentlyPlayingSongNumber);
        }

        if (currentlyPlayingSongNumber !== songNumber) {

            $(this).html(pauseButtonTemplate);
            setSong(songNumber);
            currentSoundFile.play();

            $(this).html(pauseButtonTemplate);
            currentSongFromAlbum = currentAlbum.songs[songNumber - 1];
            updateSeekBarWhileSongPlays();
            currentSongFromAlbum = currentAlbum.songs[songNumber - 1];

            var $volumeFill = $('.volume .fill');
            var $volumeThumb = $('.volume .thumb');
            $volumeFill.width(currentVolume + '%');
            $volumeThumb.css({ left: currentVolume + '%' });

            $(this).html(pauseButtonTemplate);
            updatePlayerBarSong();

        } else if (currentlyPlayingSongNumber === songNumber) {

            if (currentSoundFile.isPaused()) {
                $(this).html(pauseButtonTemplate);
                $('.main-controls .play-pause').html(playerBarPauseButton);
                currentSoundFile.play();

                updateSeekBarWhileSongPlays();

            }

            else {
                $(this).html(playButtonTemplate);
                $('.main-controls .play-pause').html(playerBarPlayButton);
                currentSoundFile.pause();
            }

        }
    };

    var onHover = function(event) {
        var songNumberCell = $(this).find('.song-item-number');
        var songNumber = parseInt(songNumberCell.attr('data-song-number'));

        if (songNumber !== currentlyPlayingSongNumber) {
            songNumberCell.html(playButtonTemplate);
        }
    };
{% endhighlight %}

## Conclusion:

The main purpose of this project was to learn and implement the front-end development topics we have learned in Bloc. I thought it was fun, thought provoking, and challenging. Since it was the first project it was fun to learn how things worked and what worked or did not work and then learning how to fix them with the tools available.
