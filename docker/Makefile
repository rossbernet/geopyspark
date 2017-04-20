.PHONY: stage0 stage1 all

SRC := archives/gdal-2.1.3.tar.gz archives/geos-3.6.1.tar.bz2 archives/lcms2-2.8.tar.gz archives/libpng-1.6.28.tar.gz archives/proj-4.9.3.tar.gz archives/v2.1.2.tar.gz archives/zlib-1.2.11.tar.gz
WHL := archives/geopyspark-0.1.0-py3-none-any.whl
JAR := archives/geotrellis-backend-assembly-0.1.0.jar
GDAL-BLOB := archives/gdal-and-friends.tar.gz
PYTHON-BLOB := archives/geopyspark-and-friends.tar.gz
VERSION := 0
STAGE0 := jamesmcclain/jupyter-geopyspark:stage0
STAGE1 := quay.io/geodocker/jupyter-geopyspark:$(VERSION)

all: stage0 stage1

archives/zlib-1.2.11.tar.gz:
	(cd archives; curl -L "https://downloads.sourceforge.net/project/libpng/zlib/1.2.11/zlib-1.2.11.tar.gz?r=http%3A%2F%2Fwww.zlib.net%2F&ts=1490316463&use_mirror=pilotfiber" -o zlib-1.2.11.tar.gz)

archives/libpng-1.6.28.tar.gz:
	(cd archives; curl -L "https://downloads.sourceforge.net/project/libpng/libpng16/1.6.28/libpng-1.6.28.tar.gz?r=http%3A%2F%2Fwww.libpng.org%2Fpub%2Fpng%2Flibpng.html&ts=1490316660&use_mirror=superb-sea2" -o libpng-1.6.28.tar.gz)

archives/geos-3.6.1.tar.bz2:
	(cd archives; curl -L "http://download.osgeo.org/geos/geos-3.6.1.tar.bz2" -o geos-3.6.1.tar.bz2)

archives/proj-4.9.3.tar.gz:
	(cd archives; curl -L "http://download.osgeo.org/proj/proj-4.9.3.tar.gz" -o proj-4.9.3.tar.gz)

archives/lcms2-2.8.tar.gz:
	(cd archives; curl -L "https://downloads.sourceforge.net/project/lcms/lcms/2.8/lcms2-2.8.tar.gz?r=&ts=1490316968&use_mirror=pilotfiber" -o lcms2-2.8.tar.gz)

archives/v2.1.2.tar.gz:
	(cd archives; curl -L "https://github.com/uclouvain/openjpeg/archive/v2.1.2.tar.gz" -o v2.1.2.tar.gz)

archives/gdal-2.1.3.tar.gz:
	(cd archives; curl -L "http://download.osgeo.org/gdal/2.1.3/gdal-2.1.3.tar.gz" -o gdal-2.1.3.tar.gz)

stage0: Dockerfile.stage0
	(docker images | grep 'jamesmcclain/jupyter-geopyspark \+stage0') || (docker pull $(STAGE0)) || (build -t $(STAGE0) -f Dockerfile.stage0 .)

$(GDAL-BLOB): $(SRC) scripts/build-blobs.sh archives/zlib-1.2.11.tar.gz archives/libpng-1.6.28.tar.gz archives/geos-3.6.1.tar.bz2 archives/proj-4.9.3.tar.gz archives/lcms2-2.8.tar.gz archives/v2.1.2.tar.gz archives/gdal-2.1.3.tar.gz
	docker run -it --rm \
           -v $(shell pwd)/archives:/archives:rw \
           -v $(shell pwd)/scripts:/scripts:ro \
           $(STAGE0) /scripts/build-blobs.sh $(shell id -u) $(shell id -g)

$(PYTHON-BLOB): $(WHL) scripts/gather-libs.sh archives/gdal-and-friends.tar.gz
	docker run -it --rm \
          -v $(shell pwd)/archives:/archives:rw \
          -v $(shell pwd)/archives/pip-cache:/root/.cache/pip:rw \
          -v $(shell pwd)/scripts:/scripts:ro \
          $(STAGE0) /scripts/gather-libs.sh $(shell id -u) $(shell id -g)

stage1: Dockerfile.stage1 $(JAR) archives/gdal-and-friends.tar.gz archives/geopyspark-and-friends.tar.gz
	docker build -t $(STAGE1) -f Dockerfile.stage1 .

clean:
	rm -f $(WHL) $(JAR)

cleaner: clean
	rm -f $(GDAL-BLOB) $(PYTHON-BLOB)

cleanest: cleaner