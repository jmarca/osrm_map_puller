To merge two states, you might try this:

```
docker run -it -w /wkd -v $(pwd):/wkd stefda/osmium-tool osmium merge  file1.pbf file2.pbf -o merged.pbf
```


So to merge illinois and indiana, I did:

```
docker run -it -w /wkd -v $(pwd):/wkd stefda/osmium-tool osmium merge illinois-latest.osm.pbf  indiana-latest.osm.pbf -o greater-chicago.osm.pbf
```

Seems to work:

```
$ ls
illinois-latest.osm.pbf  indiana-latest.osm.pbf

james at emma in ~/repos/jem/osrm-related/osrm_map_puller/data/osrm/north-america/us on feature/illinois [!]
$ docker run -it -w /wkd -v $(pwd):/wkd stefda/osmium-tool osmium merge illinois-latest.osm.pbf  indiana-latest.osm.pbf -o greater-chicago.osm.pbf

james at emma in ~/repos/jem/osrm-related/osrm_map_puller/data/osrm/north-america/us on feature/illinois [!]
$ ls -lrta
total 435096
drwxr-x--- 3 james users      4096 Feb  4 10:10 ..
-rw-r--r-- 1 james users 159633313 May 21 16:05 illinois-latest.osm.pbf
-rw-r--r-- 1 james users  63507574 May 22 13:28 indiana-latest.osm.pbf
drwxr-x--- 2 james users      4096 May 22 13:36 .
-rw-r--r-- 1 root  root  222372528 May 22 13:36 greater-chicago.osm.pbf

james at emma in ~/repos/jem/osrm-related/osrm_map_puller/data/osrm/north-america/us on feature/illinois [!]
```

Then I manually added
```
"geofabrik_download":"north-america/us/greater-chicago.osm.pbf",
```

to the package.json file.

Then, because the timestamp of that file is recent, it won't get
"downloaded" by the script (which is good because it doesn't exist on
the geofabrik server).  So I ran

```
npm run deploy_osrm_backend
```
