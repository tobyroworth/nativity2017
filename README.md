# Nativity 2017

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

As promised at the end of the credits, the code to Crown Church's nativity visuals is published here.

All visuals are based on CSS keyframe animations, with a small amount of JavaScript to advance slides and start/fade out audio. This is explained further below.

This was mostly written over a couple of days, with the credits written in the small hours on the morning of the nativity. This isn't an example of great, or production-ready, code, but got the job done.

## Content

This repository is provided to demonstarte the code only. To actually run the full show, further content is required to be downloaded

For legal reasons I can't distribute the audio, fonts or images within this repository. Links/sources to suitably sized content are provided below for reference only, and it's up to the user to ascertain whether these are legal for their use in their jurisdiction.

### audio
- [anyDream.mp3](https://play.google.com/store/music/album?id=Br7yo6wjeslrmysolf45e7ogiyq&tid=song-Tuhq3j2agohla3gpec7lwpvkree)
- [fox.ogg](https://en.wikipedia.org/wiki/File:20th_Century_FOX_fanfare.ogg)
- [jurassicPark.mp3](https://play.google.com/store/music/album?id=Ba6zfs7yccy2ltu5s2ztjt6xb44&tid=song-Tu6uspet44pv3l6ox45gyyfguny)
- [starWars.mp3](https://play.google.com/store/music/album?id=Ba6zfs7yccy2ltu5s2ztjt6xb44&tid=song-Twyb4u5vgld2i44gquhkuybj7yi)
- [thx.mp3](https://www.uspto.gov/sites/default/files/74309951.mp3)
- [whatIveDone](https://play.google.com/store/music/album?id=Bjevcdnz6jxb2j2fztmt2oajja4&tid=song-Tir7ucx6ucwoks463yljaslsv7y)

### fonts
- [Transformers.ttf](https://www.dafont.com/transformers-movie.font)

### images
- bethlehem.jpg _Google Images..._
- [earth.jpg](https://www.jpl.nasa.gov/images/earth/earth-globe-browse.jpg)
- opening.png _included in respository (c) Crown Church_
- [sheep.jpg] _Google Images..._
- [stable.jpg] _Google Images..._
- [stars1.png](http://www.starwarsreport.com/wp-content/uploads/2017/04/TLJ-1.png)
- [thx.jpg](http://cdn.iphoneincanada.ca/wp-content/uploads/2011/05/thx.jpg)

## Running the Visuals

The visuals will run locally from file URLs, or can be served using a local dev server. I used the polymer dev server on cloud9, but any http server should work.

### Requirements

A recent version of Chrome is suggested - I used 64.0.3270.0 (Official Build) dev (64-bit) or later due to [Chrome Bug 784088](https://bugs.chromium.org/p/chromium/issues/detail?id=784088), but I don't think there's anything on the blleding edge of web standards, so any modern browser should work in theory.

### Using the Polymer-CLI

First, make sure you have the [Polymer CLI](https://www.npmjs.com/package/polymer-cli) installed. Then run `polymer serve` to serve your application locally.

### Using a local file

Simply open the file in the browser.

### Usage

1. Open index.html
2. Click on `open slides window`
3. Click `start` to open the first slide
4. Click `next` to advance slides. Some slides advance on a timer

## Controller

The `index.html` file provides a thin layer of control onto the slides themselves.

The contoller and slide deck communicate using postMessage.

### `Open slides window`

This opens a new window with the slide deck. This is used the get a reference to the slideDeck window to send/recieve the messages.

### `Start`

Sends a `start` message to the slideDeck. This could have been the same as the `next` message in theory, but separate buttons were quicker to implement.

### `Next`

Send a `next` message to the slideDeck to move to the next slide. This will override a timed advance.

### Log information

On the right is log data coming back from the slides window. This was maoinly used for development, but could be useful if the main visuals couldn't be seen.

## Slide Deck

Each slide was a significant amount of development in itself. Each slide uses only HTML and CSS, except for the large credits where I got lazy and used a tiny amount of JavaScript to advance the sub-slides.

### Architechture

Each `<div class="slide">` represents a slide, with a descriptive ID. Each slide represents an animation sequence, optionally with audio.

In the script there is a `slides` array that gives extra information about the slides. Ideally this would have all been declared in the slides themselves, probably using an `HTMLCustomElement` with a timing property, but this way was the path of least resistance. It's important the the order matches the order of the HTML, and that no slides are missing from this array. Again, this would be better accomplished using a custom element and running querySelectorAll to get the list of slides.

Each slide is allowed up to one audio track, which is played if it exists, and fades out on slide change.

All animations are performed on `opacity` and `transform`, to keep the slideDeck performant by avoiding paint.

Animations `animation-play-state` property is set to `inherit`, so that setting it to `running` on the slide itself starts all animations in the slide. For animations that start later in a sequence, they are simpley delayed. Note that the default of `animation-play-state` is `running`, so all intermediate objects need to the set to `inherit` as well, otherwise the slides play state stops cascading. On slide advance the slide's play state is set back to `inherit`.

When the slide is advanced, the slideDeck container gets transformed up an additional 768px, which results in the old slide being outside the clipping boundary of the outer container. Maybe setting the expired slide to `display: none;` would have been better.

Once the slide has started, two promises are raced, one that resolves once a time has elapsed, and another that waits for a manual advance event. This event can come from a click on the slides window, or a message from the controller. This allows timed slides to be easily skipped, which was incredibly useful during development. There is a ~~special case~~ bodge for the credits, described below.

It should be noted that [Promise.race isn't ideal](https://www.jcore.com/2016/12/18/promise-me-you-wont-use-promise-race/), as if either of the promises rejects then the slide will immediately advance. For my use it was _good enough_, but I wouldn't use it like this in production.

### `.flexCheat` class

This class centres whatever's within it to the slide, ignoring what's going on around it. This is used when stacking centred items. It feels like a bodge, but did what I needed it to.

### Generic image slides

Some scenes in the natvity needed background scenes. These have a short fade in applied, but are otherwise uninteresting. They would have faded out, but this required a little too much code, given that I did this bit the day before the nativity. It's not hard to do, as a property could be check on slide advance and an extra animation dynamically added to the slide and started, but it just required too many changes when I still had the credits to program.

### THX

This is just a couple of simple keyframe animations, running on top of one another. These were timed to match the YouTube video of the original Star Wars opening.

The logo itself has a slight pulsing, which tried to mimick the flashing in the original. This was much quicker than animating real twinkley bits.

### Fox

The fox logo slide contains two animated sections, because the music had to continue over them.

The fox logo is probably the bit I'm least happy with. I didn't want to resort to photoshop to make the logo, as I'd managed all other content in CSS, so did the text shadow trick to give the text depth. This is then transformed to give a 3D effect.

The beams of light are just rectangles that have some 3D transforms applied. I'd love to say I carefully calculated all the offsets and perspective, but ended up just throwing numbers at it until it looked about right.

The background wanted a little more work, ideally, but the basic concept is there - stacking a few gradients until the look like clouds and stuff.

The Luka'sfilm logo then fades in after the fox one fades out. There's nothing special there. I toyed with doing the newer style logo, that fades out to grey, but this was a lot of work, and less true to the Episode 4 look I was going for.

### A long time ago

Again, this is really simple. The delay at the end is left deliberately longer than usual, mainly to scare the guys at the back.

### Opening Crawl

This got the most attention, as it's the main feature of this scene in the nativity.

A basic zoom effect is used on an svg star wars logo, which gives way to a perspective-transformed box of justified text.

This text was a nightmare to get right and, to be honest, still isn't perfect, and I never got around to using the right typeface. This was unforgivable and, unfortunately, will lead to ridicule by my mate André.

During developemnt, I had a lot of trouble with the text not displaying until it was partway through its animation. I thought this was due to the text being behind the camera, or something like back-face visibility (it clearly wasn't). Then, after several frustrating hours of mucking around, I tried putting a background on the box, and the background showed, but the text didn't. _Hmmm._ The commented out section in the `crawler` animation was a workaround that, somehow, got the text to render earlier. I ended up submitting a bug report ([Chrome Bug 784088](https://bugs.chromium.org/p/chromium/issues/detail?id=784088)) and the Chromium folks, as you can see, were very responsive. The next update fixed it, so I could comment out the workaround (my faith was shaken enough to not delete it though).

At the end, the famous "pan down" happens (which is actually a slide up, anything more didn't seem worth the effort).

### The Wrong Joseph

Given some music to play, I felt more rainbow was needed, so spent far too long trying to gte a rainbow-cycling effect to work. As is my want, I didn't want to animate the gradient, as it's potentially going to lead to lots of painting.

The tricky part was getting the start end end to line up. I did lots of trig, but couldn't get it to work at an angle, so in the end I gave up and just ran it sideways. Later that night, I found the diagram that explains how gradient positioning works, but by then had moved on. I've since lost that page again.

### Large Credits

The credits are modelled on the Transformers credits, as Michael Bay was one of the characters in the production. Due to the audio getting faded out after each slide, these had to use their own little slide system, rather than each be a full-blown slide. This also saved adding _a lot_ of slides to the slides array.

Had I not been doing this very late at night, I would've added a hook to take a custom advance function. However, I was doing this late at night, so I hard-coded a bodge in.

The way these slides advance is different to how the main slides advance, as they have a known animation length. It's probably closer to how I'd animate the main slides if I started again, except I'd remove the `shown` class when done to remove them from the render tree, I think.

The slide length is just the music length divided by the number of slides, then fudged to make it look right.

Had I had more time, I'd've liked to add in a little twinkle, like on the [original credits](https://youtu.be/0XYjpcLTjKg). Ideally this would happen in a random place on each bit of text, which would be tricky to calculate. Doable, but not something to be attempted at 2AM.

I would've also likes to add in an extra little video scene during the credits, but didn't have any suitable videos, and didn't have any time to film one.

### Small Credits

Again, this is a simple transform. A margin is set to the credits start off-screen, and they scroll and extra 10% to ensure they end up off the screen. This would've been better done with `calc()` to add `768px` twice.

The credits are filled with little in-jokes and homages to the great people who serve us on a Sunday morning. I've removed some of these from the repo to avoid any data/child protection issues.
