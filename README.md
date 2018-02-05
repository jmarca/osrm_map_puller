# OSRM map puller

This is a pretty simple repo that just pulls OSM map files and builds a Docker
image of OSRM server that contains that data.  It does this by scripting the
steps that are listed in the README for the [OSRM
Backend](https://github.com/Project-OSRM/osrm-backend) project.

In addition, it borrows a hack I developed for the CalVAD project to
[conditionally download a file based on its
age](https://contourline.wordpress.com/2016/05/26/how-to-use-npm-to-conditionally-download-a-file/).
And all this is done from withing the npm package.json file.  This is probably
dumb, and I probably should use a Makefile rather than npm, but it does the job
and is reasonably well supported everywhere these days, so I'm sticking with
npm.

This also uses a lot of bash scripts inside the npm scripts, so this probably
will not work on windows.

# Oregon or something else

Right now it is set up to download Oregon.  To change that, you can either edit
the package.json file directly, or else you can use npm config hackery to do the
job.  A better way is to pass a command line to the npm run command, but I
haven't set that up yet.

Look at the [listing of files on geofabrik](http://download.geofabrik.de/north-america.html) to determine what path to change the
`geofabrik_download` variable to equal.  For example, for California, you would
enter

```
...
   "geofabrik_download":"north-america/us/california-latest.osm.pbf",
...
```

There are also most of the pieces here to setup doing something like cutting out
the continental united states from the geofabrik download of all of north
america.  I didn't operationalize that because when I tried it my server
crashed...it needs a lot more than 64GB of RAM to process all of USA into a
network, I guess.  Besides, it makes more sense to generate containers for, say,
the four parts of the USA (West, Midwest, Northeast, South) available on
geofabrik, or one per state, etc.

Lots of improvements can be made to this.  This is just a hack for me to help
dockerize an OSRM container with data.

# Running things

First off, Docker must be installed.  Full stop if you don't have that.

Second, look at the package.json to see the gory details, but things should just
work if you first run:

```
npm run deploy_osrm_backend
```

That will download the specified OSM data (Oregon by default), download the OSRM
backend container, and then use that container to process the downloaded OSM
data.  Then once that is done, it will create a new docker container locally
called something like `jmarca/osrm-preloaded-backend:2018-02-04T10-13-03`, where
the datetime string at the end is determined by the date of the downloaded
OSM.pbf file.  I *should* add the file itself to that (Oregon, California,
US-Midwest, etc) but I just realized that now.  Future edition.

So the idea is that you can use these Docker images on a number of machines, and
they will all have the identical source OSM.pbf file, because of the identical
date-time stamp in the version.  When you grab a new OSM.pbf file (by re-running
the npm script after a month has gone by, say), then new docker images will be
created with new datetime-based versioning.

To run and deploy the canonical OSRM front end, all you have to do is run

```
npm run deploy_osrm_frontend
```

This will launch the front end server.  It will also center the map on Oregon at
zoom 10.  To change this to a different center or zoom, twiddle the dials in the
package.json file:

```
...
   "osrm_frontend_center":"45.418214,-122.706985",
   "osrm_frontend_zoom":"\"10\"",
...
```
