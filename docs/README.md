# Extract geojson coordinates from wikipedia files

Wikipedia2geojson reads wikipedia files and prints out geo locations.  

It can read compressed files, and compressed streams.

# Example

        wikipedia2geojson.exe file.xml.bz2

Reads from file.xml.bz2, automatically uncompressing bz2 format

    [
    { "type": "Feature", "geometry": { "type": "Point", "coordinates": [ 2, 28 ] }, "properties": { "name": ""Algeria"" } }
    { "type": "Feature", "geometry": { "type": "Point", "coordinates": [ 30, 42 ] }, "properties": { "name": ""Andorra"" } }
    { "type": "Feature", "geometry": { "type": "Point", "coordinates": [ -150, 64 ] }, "properties": { "name": ""Alaska"" } }
    { "type": "Feature", "geometry": { "type": "Point", "coordinates": [ 19, 13 ] }, "properties": { "name": ""Apollo 11"" } }
    
Each location is on its own line, so you can pipe this stream into grep and other command line programs.  

# Streaming

Because w2j can read compressed streams, you can process network files on the fly.  You don't need to download them completely first.

    wget -q -O - http://someserver.com/enwiki-pages-articles2.xml.bz2 | wikipedia2geojson --compression=bz2 -

# Installation

        go get -u github.com/donomii/wikipedia2geojson
      
# More examples


        wikipedia2geojson.exe file.xml

                Read from file.xml


        wikipedia2geojson.exe file.xml.bz2

                Read from file.xml.bz2, automatically uncompressing bz2 format


        wikipedia2geojson.exe file.xml.gz

                Read from file.xml.bz2, automatically uncompressing gz format


        wikipedia2geojson.exe --compression=bz2 file

                Read from file, force uncompressing bz2 format


        wikipedia2geojson.exe --compression=gz file

                Read from file, force uncompressing gz format


        wikipedia2geojson.exe -

                Read from stdin.


        wikipedia2geojson.exe --compression=bz2 -

                Read from stdin.  Stdin is in bzip2 format


        wikipedia2geojson.exe --compression=gz -

                Read from stdin.  Stdin is in gz format

# Known bugs

You can't stream straight from the network on MS Windows, because Windows fiddles with the data as it goes through the pipe, and most download programs don't know how to stop that.

So this won't work

    wget -q -O - http://someserver.com/enwiki-pages-articles2.xml.bz2 | wikipedia2geojson.exe --compression=bz2 -

# Bonus

A perl one liner to unpack the wikipedia geodata files in sql format

    type enwiki-20171103-geo_tags.sql | perl -pe "s/\),\(/\r\n/g" | perl -ne "@c=split/,/;if($c[8]ne'NULL'){print '{ \"type\": \"Feature\", \"geometry\": { \"type\": \"Point\", \"coordinates\": [ '.$c[4].', '.$c[5].' ] }, \"properties\": { \"name\": '.$c[8].' } };'.\"\n\";}"
    
Wikipedia's geodata extraction appears to have trouble identifying points, so wikipedia2geojson will be useful for a bit longer yet.

