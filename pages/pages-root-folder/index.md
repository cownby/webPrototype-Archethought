---
# Landing Page
#
# Use the widgets beneath and the content will be
# inserted automagically in the webpage. To make
# this work, you have to use › layout: frontpage
#
layout: frontpage
title: "Archethought"
header:
  image_fullwidth: gear-wallpaper-1600x507-dark.jpg

widget1:
  title: "Micro-location"
  url: "/products/shepherd/"
  image: borrowed/clker-free-binoculars-40881__340.png
  text: 'Where IS Everybody?!?'
widget2:
  title: "Solutions "
  url: '/products/'
  image: borrowed/pixabay-board-1521348__340.jpg
  text: 'We define innovation as the identification and removal of bottlenecks. That is what we do! '
widget3:
  title: "Customization"
  url: '/products/customization'
  image: CosolveUnit1-5-340w.jpg
  text: 'We <em>love</em> tech, and are dedicated to open source solutions.'
#
# Use the call for action to show a button on the frontpage
#
# To make internal links, just use a permalink like this
# url: /getting-started/
#
# To style the button in different colors, use no value
# to use the main color or success, alert or secondary.
# To change colors see sass/_01_settings_colors.scss
#
callforaction:
  url: '/about'
  text: Our heritage consists of expertise across many aspects of data science, artificial intelligence, machine learning, software and hardware engineering, and full stack web development. We are a community focused organization with great enthusiasm towards innovations that improve the daily lives of our citizens.
# five different kinds of alerts: warning (orange), info (blue), success, alert (red) and text (blue).
  style: info
#  
permalink: /index.html
#
# This is a nasty hack to make the navigation highlight
# this page as active in the topbar navigation
#
homepage: true
---
