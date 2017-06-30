---
layout: post
title:  "Project Debrief: Creating a CLI Gem"
date:   2017-06-30 16:04:47 +0000
---


The assignment: create a CLI application that scrapes data from a website to produce both a list view and a detail view based on user requests. 

Flatiron provided two sample projects to work around, plus some videos with helpful tips. I was so excited to get started I began browsing around looking for a website that I could easily scrape. Weirdly, this is one of the challenges I usually have the most trouble overcoming: What is the basic thing I want my program to do?

My initial ideas crashed into a wall of [unscrapable Javascript](http://www.active.com/running/marathon), but I eventually stumbled on to Alexa.com. Alexa, an Amazon company, presents information about the most used websites and their usage statistics. Bam, let's get going.

I forked and reviewed the sample projects provided, [World's Best Restaurants](https://github.com/dannyd4315/worlds-best-restaurants-cli-gem) and [Now Playing](https://github.com/learn-co-curriculum/now-playing-cli-gem). These are basic gems that allow a user to see a list of restaurants or movies, then drill down to see more specific movie about each respective target. They are simple enough, with architectures similar to what I've been reviewing in the Flatiron curriculum.

I fired up a new Github repo and started planning. The endpoints I could target included a listing of the top 50 most used websites or top websites in a country. I started planning a crazy CLI: a user can enter a website, then see every statistic, and see what countries it's popular in, and then the other websites popular in those countries, and....! Slow down, Brian. I realized this project has to be submitted eventually, so I scaled back.

With a Trello board, I refocused on stories. 

* A user will be presented with a list of websites.
* A user will be asked if they'd like more information about one of the websites.
* Upon user selection, a user will be able to learn more about a website and its statistics. 
* A user can return to the list of websites to study others, or exit. 

Let's code. 

I used bundler to spin up a gem architecture. Unfortunately, this also created a Github repo within the repo I already created. This is a problem I have yet to solve as of this writing. Based on the Now Playing gem, I realized I only needed three main classes: a CLI, a Site object, and a Scraper. 

**cli.rb**

The cli has a very basic job: get user input and present scraped data. I tried to create a nice presentation for the data I was scraping, but I am not well versed in ASCII art. Easily, I settled on a design: a #run method that calls the #start method, which loops until exit, prompting the user about choices.

**scraper.rb and site.rb**

Here comes trouble. I imported Nokogiri and Open-URI so I could scrape and interpret Alexa. Even with my scaled down plan, I still had a lot of little pieces of data to hunt down. Originally, I created the scrape index function to return an array of names of websites (in my app, a site name is also its URL, like Google.com). This was fine to begin with, but it caused a problem later on.

Per the spec presented by Flatiron, I was supposed to create a list of objects--not hashes. So I was going to have to create an object from each name I found on the list. Originally, I had the scraper gather all the names in the index, then create a site object from each name. The first time I ran this code I thought Terminal might be hanging. In reality, I was making fifty calls to fifty websites and scraping data from each--of course it was gonna slow down.

So I refactored my site code. I could init a site object with a hash, and then #send the site each key of the hash. On a key that matches a property, the property will take on the key's value. Additionally, I included an #update_attributes method that could popular ariables with the contents of a hash. In fact, the init method called this one. Now we could instantiate an array of site objects as above, so they would be site objects with names only. When the user called for more data on the site, we could populate it with a call to scrape data. Cool! 

```
  def initialize(site_hash)
    update_attributes(site_hash)
  end

  def update_attributes(hash)
    hash.each {|key, value| self.send(("#{key}="), value)}
  end
```
