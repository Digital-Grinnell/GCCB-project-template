# Notes from Slack

This is just a document I created to save Slack conversations about CB before they vanish.  

## Azure Blob Storage for Oral Histories

This thread from Slack is already stale and the opening parts are missing, so here's a quick summary of the issue at hand...  

I store oral history objects, mostly .mp3 files, in Azure Blob Storage and serve them to CollectionBuilder, but on more than one project I've noted that the timestamp links in the transcript that CB display are unreliable.  Sometimes clicking one of those links take me to the correct spot in the playback, but frequently it does NOT.  

Devin correctly identifed the problem as specific to Azure Blob Storage (and some other services), but indicated that platforms like AWS and DigitalOcean did not have the same issue.  Devin also mentioned a configuration change with Azure that might solve the problem, but those details have been lost.  

The portion of the conversation that is still intact follows here...   

Ah, and I'm in Azure Blob Storage.  I'll try the option 1 updates and see what happens.  By the way, Claude has become a good friend of mine, too.  :grinning:

As for the deployment problem...   The Digital-Grinnell/GCCB-template-project has been updated so it now deploys to https://gentle-ground-0283de21e.3.azurestaticapps.net/ in an environement that include TZ: America/Chicago.  If you look at the footer of any page you should see when the last https://gentle-ground-0283de21e.3.azurestaticapps.net/ was built.

Just got around to testing this... Azure Blob Storage is returning the entire object, not the 206 Partial Content that's needed.

We also have some CDN space in DigitalOcean Spaces.  I copied one of our .mp3 files there and did some testing... it works BEAUTIFULLY.

So I think that errant timestamp jumping within the playback can be easily addressed.  Thank you @Devin Becker (and Claude)!

You can see the result for this one oral history at https://calm-ocean-061ff161e.1.azurestaticapps.net/items/dg_1750784116.html.