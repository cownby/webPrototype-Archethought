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
  text: 'Solutions that point the way (link to base products page with other image options)'
widget3:
  title: "Customization"
  url: '/products/customization'
  image: borrowed/pixabay-gear-1015715__340.jpg
  text: 'We are dedicated to open source'
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
#  url: https://tinyletter.com/feeling-responsive
  text: This is an optional callout designed to link to something - contact, feedback, more info, etc.
  style: alert
#  
permalink: /index.html
#
# This is a nasty hack to make the navigation highlight
# this page as active in the topbar navigation
#
homepage: true
---
