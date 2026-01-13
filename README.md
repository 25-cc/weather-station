# Weather Station
#### Video Demo:  <URL HERE>
#### Description:
##### Overview
Weather Station is a website built using Jekyll and written in HTML, CSS and JavaScript to fetch and display the latest weather readings and forecasts for Singapore.

##### Template (default.html)
All pages on the site use a single template, default.html. It has the following features:
- Inclusion of the important libraries needed on each page automatically (Bootstrap and Leaflet as well as a custom CSS stylesheet)
- A navigation bar to allow users to navigate the website easily
- A footer with credit and copyright notice

As Jekyll uses the Liquid templating language to process templates, it also includes placeholders to be filled by each individual page - namely, the page title and content.

##### Home Page (index.html)
The home page is the first page users will see when navigating to the website. 

When the website loads, it starts by executing a JavaScript function to fetch the weather APIs provided by Singapore's National Environmental Agency. As the weather readings come from six separate APIs, they have to be fetched individually, which results in a noticeable delay before the rest of the script runs. I put a loading spinner at the top of the page to let users know that the webpage is actively working and not frozen or buggy. This spinner will disappear after the rest of the script is executed.

Afterwards, the script will attempt to get the user's location using navigator.geolocation.getCurrentPosition. If the user visits the webpage on a device with GPS functionality (such as a smartphone), the browser will prompt the user if they want to allow the site to access geolocation data. The script will then get the user's latitude and longitude. However, if permission is not given, or if the device does not have any GPS functionality (such as a desktop), the script will log the error in the console and assign the user a default location in the center of Singapore. A map is also rendered on the page using Leaflet, centered on the user's latitude and longitude.

With the user's latitude and longitude, the script proceeds to get weather readings using getWeatherReadings(). As each weather API uses a slightly different set of weather stations, the following steps are repeated for each dataset:
1. Find the nearest weather station to the user using the Haversine formula (a formula used to calcuate distances on a sphere given two sets of coordinates).
2. Update the page HTML with the relevant weather reading and weather station name.
Once this has been done, the webpage is fully rendered! The loading spinner at the top of the page disappears by adding "hidden" to its CSS using style.visibility.

The webpage is split into three sections.
1. The first section is used to display weather readings from the APIs: weather, heat readings, UV index and air pollution.
2. The middle section displays a map of the user's current location. It is also used if the user wants to change location to another place (detailed below).
3. The final section is an accordion that hides extra features, such as the exact weather stations used for the readings and some health and safety information. There is also a function to change location if the GPS location is incorrect or if the user wishes to see the weather at another part of Singapore.

To change location, the user must do the following two steps:
1. Pick a location on the map in the middle section of the webpage. If this is not done, the page will display an toast error message to prompt the user to select a location first. This is detected by the variables mapLat and mapLon, which are declared in the global scope of the script but are not defined until the user clicks on a map location. Hence, if the variables are still undefined, it means that the user has not clicked on the map at least once.
2. Click the button that says "Set new location" and click OK on the prompt that appears. If cancelled, a toast message will prompt the user to reselect their location. If successful, getWeatherReadings() is called once again.

To ensure that the updated readings are obtained as fast as possible, I made the design decision to perform all API calls only once upon page load. If I were to call the API every single time the user changes readings, it would be extremely slow (on the order of several seconds) and would run into API rate limit issues fairly quickly. The data from the API is also updated at most every 5 minutes, which has little bearing on the accuracy of the information shown. I also made sure to give several visual cues to the user that their location has been updated - by displaying an alert below the button once their location has been successfully updated, as well as moving the marker on the map from their old location to their new location.

##### 24-hour forecast (24h.html)
The 24-hour forecast displays the weather forecast for the next 24 hours.

In the API, two sets of data are provided: 
1. Overall readings for the whole of Singapore (forecast, temperature, humidity and wind speed) - this is rendered at the top of the page.
2. Location-based weather forecasts for central, north, south, east and west Singapore - this is rendered on a Leaflet map instance. Markers corresponding to each location are placed on the map, and if the user hovers over a marker, they can see the forecast for the next day for that location in a tooltip.

##### 4-day outlook (4d.html)
The 4-day outlook displays the weather forecast for the whole of Singapore for the next 4 days. Similar to the 24-hour forecast, overall readings for the whole of Singapore (forecast, temperature, humidity and wind speed) are given by the API, which are then rendered on the page. As the outlook changes every few hours, I added the forecast timestamp provided by the API at the top of the page.

To render the dates in a human-friendly format, I used the JavaScript constructor Intl.DateTimeFormat.

##### About page (about.markdown)
The About page provides attribution and credits for all the data and assets that originate from other sources.

One interesting feature about Jekyll is that pages can be written in Markdown and rendered in HTML afterwards. As the about page is simple, static and does not require any JavaScript or CSS customization, I used Markdown to construct this page to make the process simpler for me.

##### 404 page (404.html)
This page is simple. It's a 404 page if the user ends up at any page that they aren't supposed to be at. Which is unlikely, given the limited pages of the webpage and the functional navbar!
