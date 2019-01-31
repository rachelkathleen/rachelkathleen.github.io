---
layout: post
title:      "DiveReport - CLI Final Project"
date:       2019-01-31 04:17:09 +0000
permalink:  divereport_-_cli_final_project
---


The first big hurdle was finding a 'scrapeable' site.  After identifying a good [site](http://divereport.com) and having a clear idea of what I wanted my gem to do – I started inspecting the page and using Nokogiri to find the CSS selectors to scrape to create the objects for the four classes in my gem - `Animal`, `Region`, `Country` and `DiveLocation`.

The site directory had had the four headings (and my four future classes) in one class – `.headings`. All of my future objects were in one class – `.sitemaplist`. 

[img]https://i.imgur.com/Jv5PyJV.png?1[/img]

I forked a copy of this [Repl.it](https://repl.it/@jenn_leigh_hansen/ScraperChecker?language=ruby) to quickly figure out which indexes were animals, regions, countries and dive locations and wrote a method that would iterate over the entire array or nodesets that corresponded with `.css("ul.sitemaplist li a")`.

This become the first method in the `Scraper` class:

```
def self.scrape_directory_site
    html = open('http://www.divereport.com/site-directory/')
    page = Nokogiri::HTML(open(html))

    objects = page.css("ul.sitemaplist li a")
    objects.each.with_index do |nodeset, i|
      url = nodeset.attr("href")
      name = nodeset.text
      if i < 31
        Animal.new(name, url)
      elsif i < 44
        Region.new(name, url)
      elsif i < 126
        Country.new(name, url)
      else
        DiveLocation.new(name, url)
      end
    end
  end
```

This method identifies two elements in each nodeset (the url, and the text), and passes the values in are arguments in the initialize methods for each class.

Now that I had four classes of objects – and a start to the scraper class, I started to write my CLI class. 

I knew where I wanted my program to end – a display of information about each dive location. I wrote a stub method for that - `#print_location_details`, and worked backwards from there. 

What would come before the location details were printed? The user would need to pick a location!  `#dive_location_input`. 

After building back to the beginning via the `Animal` class, I did the same for  `Region` and `Country`.

Once I had completed a first draft of the pseudo-code in the CLI class – I went back to scraping to find the CSS selectors for my class attributes, and wrote the methods I would need to make the classes relate to each other. 

After several rounds of refactoring – here is the link to the repo on [GitHub](http://https://github.com/rachelkathleen/DiveReport).







