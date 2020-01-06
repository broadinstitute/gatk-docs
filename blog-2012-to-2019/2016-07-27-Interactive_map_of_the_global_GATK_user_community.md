## Interactive map of the global GATK user community

By Geraldine_VdAuwera

<p>This morning, we unveiled an <a rel="nofollow" href="https://software.broadinstitute.org/gatk/usermap.php">interactive GoogleMap</a>, based on anonymized IP addresses collected from the forum database, that shows how the GATK user community is distributed across the globe. Check out Boston/Cambridge!</p>

<p>For the record, this was originally inspired by the <a rel="nofollow" href="http://omicsmaps.com/">World Map of High-throughput Sequencers</a> by James Hadfield (Cancer Research UK, Cambridge) and Nick Loman (University of Birmingham).</p>

<p>As several people have already expressed interest in how this map was put together, I thought I'd give a brief overview of the technical side below the fold. I'm happy to provide more details and/or code if anyone wants to do something similar.</p>

<hr></hr><h3>Making the map</h3>

<p>First, I retrieved the IP addresses recorded in the forum user database (last known IP only, to avoid inflating the counts) -- <strong>without any associated user information</strong>, so the IPs are anonymous. I ran them against the free <a rel="nofollow" href="http://dev.maxmind.com/geoip/geoip2/geolite2/">MaxMind GeoLite2</a> geolocation database to look up the corresponding approximate location (using their <a rel="nofollow" href="http://dev.maxmind.com/geoip/geoip2/downloadable/#MaxMind_APIs">delightfully simple API</a>), which gave me the City, Country and geographical coordinates (Latitude and Longitude) for each. Then it's just a matter of counting how many records we have for each City+Country pairing, and consolidating that information into a JSON file to feed to the <a rel="nofollow" href="https://developers.google.com/maps/">GoogleMaps API</a> that actually draws the map. The responsive clustering of the markers, which is the really cool feature here, is done by a nifty little Javascript library called <a rel="nofollow" href="https://github.com/googlemaps/js-marker-clusterer">markerclusterer.js</a>.</p>

<h3>Problems and perspectives</h3>

<p>The map represents data from ~25,000 registered users out of the ~36,000 total. This does mean we're missing a sizeable chunk of the community, and it's because many IPs were either not in the free version of the geolocation database that I used, or the location was not associated with a name. Funny enough, due to a bug in the first version of my script, the unnamed records were all getting assigned to a single pair of coordinates, so the map was showing over 8,000 GATK users way out in a remote part of Australia. Had me wondering whether the Garvan Institute was hiding a massive secret facility out there!</p>

<p>In a future iteration I think I can salvage the unnamed records by consolidating based on coordinates instead of City+Country name, the choice of which in hindsight was not a great design decision. Right now I'm also not handling correctly any cases where the same city name exists in several states within the United States -- as a born-and-bred European person, I assumed that the City+Country name pair is unique, but now that I think about it, it doesn't hold true in the USA, does it... I don't think this would affect a large number of records, but hey, we care about accuracy, so further refinements will be forthcoming!</p>
