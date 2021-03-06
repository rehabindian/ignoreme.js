ignoreme.js
===========


Sometimes, you just don't want your visit to count. 

A medium-traffic blog or e-commerce site may be visited by any number of 
administrators, guest contributors, and employees over the course of a day. 
These visits may be feature tests, post content previews, or even on-site live
demos  for potential clients. The resulting "junk" hits can really mess with 
analytics stats if they constitute a nontrivial proportion of your traffic. 

Google Analytics has some official filtering and traffic exclusion 
functionality to deal with unimportant visits, but it's all pretty rigid[1] and
usually involves some kind of klunky workaround techniques[2] to properly "ignore"
junk traffic. The alternative is to use GA-blocking browser plugins, but those
only work in the computers (and browser executibles) where they are installed.
And it's a pain to switch them off and on during development.

ignoreme.js is a small Javscript utility that can selectively tell Google 
Analytics information about the current visit based on URL parameters. By 
placing it in the page where the Google Analytics code would normally go (in the
head or at the end of the body), admins can choose at "browse-time" whether to
ignore a page visit outright or merely tag it to be filtered out later.


========================
Installation:
========================
Include ingoreme.js and then create an IgnoreMe object using inline scripts 
where you would normally put GA code (as shown below).

========================
Example usage:
========================
The following code is put into the "stuff.html" page of example.com/.


	//...
	ops = { gaId: "UA-XXXXXXXX-X", 
			signal: "get", 
			level: "paranoid",
			key: "notrack",
			value: "yes"
		  };
		
	ignorer = new IgnoreMe(ops);
	//...


If someone goes to "http://example.com/stuff.html", the above code treats it
as a normal, GA-tracked visit by telling Google about it. If, however, a user 
types (or is linked to) "http://example.com/stuff.html?notrack=yes", then 
ignoreme.js won't load request any GA-related assets from google. The visit will
also not appear in GA's Real-Time feed.

Here's a slightly more conventional example that uses Google's recommended 
filtering techniques[1] (instead of paranoid blocking) and a different signal:


	//...
	ops = { gaId: "UA-XXXXXXXX-X", 
			signal: "hashbang",
			level: "filter",
			value: "shush",
		  };
		
	ignorer = new IgnoreMe(ops);
	//...


Now, when someone types "http://example.com/stuff.html#!shush", ignoreme.js will
fetch and build all GA-related assets, but it will mark the visit using a GA
"custom variable". If the site admin has configured GA to ignore traffic
with that variable, then those visits will not be evaluated in any metrics
(but the visit will still be visible to whomever is watching the Real-Time 
graph).


========================
FAQ:
========================

Q: Won't this make it easier for people to mess with my analytics? Will users be able to hide their visits more easily?
--
A: Technically speaking, sorta. Practically, no. Anyone with their browser
dev console open or a GA-blocking plugin (like Ghostery) can already skew 
your visit statistics, and the latter of these two is probably a much 
greater concern if you're aiming for 100% coverage. ignoreme.js doesn't
contribute much to either side of the "analytics arms race."


Q: What do I need to do in my Google Analytics dashboard?
--
A: If you run ignoreme.js in paranoid mode, nothing. The other modes require only
minimal setup to fully filter results. You may be as elaborate or as sparse in
your config as you wish. For instance, it might be prudent to eliminate GET
parameters from your analytics[3].


Q: There are multiple people who work on my site. How do I tell them about this?
--
A: After you've installed it on all the pages they will visit, simply give
them the URL fragment (the hashbang or the GET parameter) and ask that they
append it when viewing pages.


Q: Can I still use GA like I normally would? Does event logging and all that other stuff still work?
--
A: Yep. ignoreme.js puts the same variables into global scope that normal GA
asynchronous code does (namely ga and _gaq), and as long as you place it
where you'd normally put GA code, it'll be all gravy.


Q: What dependencies does ignoreme.js have?
--
A: None.


Q: Is this tool bug-free, well-documented, or well-supported? Is there any warranty, express or implied? Are you or the software in any way related to Google?
--
A: Nope. 


  [1]: http://support.google.com/googleanalytics/bin/answer.py?hl=en&answer=55481 "Google - How do I exclude my internal traffic from reports?"
  [2]: http://www.mcbuzz.com/2011/how-to-exclude-visitors-from-google-analytics/ "McBuzz - How to Exclude Internal Visits/Visitors from Google Analytics Reports Using Asynchronous Code"
  [3]: http://iqcontent.com/blog/2007/06/dealing-with-query-strings-in-google-analytics/ "iQ Content - Dealing with query strings in Google Analytics"
