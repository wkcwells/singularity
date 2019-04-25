# Use an official Python runtime as a parent image
FROM nvidia/cuda:8.0-cudnn5-devel-ubuntu16.04

# Set the working directory to /app
#WORKDIR /app

# Copy the current directory contents into the container at /app
#COPY . /app

# Install any needed packages specified in requirements.txt
#RUN pip install --trusted-host pypi.python.org -r requirements.txt
RUN apt-get -y update
RUN    apt-get -y install software-properties-common
RUN    add-apt-repository ppa:saiarcot895/myppa
RUN    apt-get -y update
RUN    apt-get -y install aptitude
# Note: removed mercurial from here because custom g4py does not require it
RUN    apt-get -y install python-pygame python-matplotlib python-virtualenv build-essential xorg-dev python-dev libglu1-mesa-dev  freeglut3-dev uuid-dev liblapack-dev git subversion libatlas-base-dev libbz2-dev
RUN    apt-get -y install wget curl cmake libboost-all-dev libsdl1.2-dev  libatlas-base-dev libxerces-c-dev libglu1-mesa-dev freeglut3-dev mesa-common-dev
RUN    apt-get -y install libx11-dev libxpm-dev libxft-dev libxext-dev libgsl-dev libpng-dev libjpeg-dev gcc g++
RUN    apt-get -y install vim htop
RUN    apt -y upgrade      # This was necessary to install Emacs

ENV    APP_DIR /app/
RUN    mkdir $APP_DIR
RUN    chmod a+rwx $APP_DIR
RUN    cd $APP_DIR
RUN    touch .aksetup-defaults.py
RUN    echo "BOOST_INC_DIR = ['/usr/include/boost']" >> .aksetup-defaults.py
RUN    echo "BOOST_LIB_DIR = ['/usr/lib/x86_64-linux-gnu']" >> .aksetup-defaults.py
RUN    echo "BOOST_PYTHON_LIBNAME = ['boost_python-py27']" >> .aksetup-defaults.py

#Setup virtualenv
RUN    virtualenv --system-site-packages $APP_DIR/chroma_env
ENV    PATH /usr/local/cuda/bin:$PATH
ENV    LD_LIBRARY_PATH=/usr/local/cuda/lib64/:$LD_LIBRARY_PATH

# I don't think environment variables set here will propagate
RUN    . $APP_DIR/chroma_env/bin/activate

# Seems like we have to do this manually
# I think what's going on is that every command executed in a separate shell, so there is no notion of a current working directory?
ENV    VIRTUAL_ENV $APP_DIR/chroma_env
RUN    echo $VIRTUAL_ENV
# Root got downloaded and untarred intot he root filesystem.  So VIRTUAL_ENV must not have been set.
RUN    mkdir $VIRTUAL_ENV/src/

#Install ROOT
WORKDIR   $VIRTUAL_ENV/src
RUN    wget https://root.cern.ch/download/root_v5.34.38.source.tar.gz 
RUN    tar -xvf root_v5.34.38.source.tar.gz
RUN    mv root $VIRTUAL_ENV/src/root-5.34.38
WORKDIR   $VIRTUAL_ENV/src/root-5.34.38
RUN    ./configure --enable-builtin-freetype --disable-fftw3 --with-x11-libdir=/usr/lib/x86_64-linux-gnu --with-xpm-libdir=/usr/lib/x86_64-linux-gnu --with-xft-libdir=/usr/lib/x86_64-linux-gnu --with-xext-libdir=/usr/lib/x86_64-linux-gnu --with-gsl-libdir=/usr/lib/x86_64-linux-gnu --disable-asimage
RUN    make -j8
RUN    echo ". $VIRTUAL_ENV/src/root-5.34.38/bin/thisroot.sh" >> $VIRTUAL_ENV/bin/activate

#Install GEANT4 - Note: we are no longer installing cmake - the base build should have it
WORKDIR   $VIRTUAL_ENV/src
RUN    wget http://geant4-data.web.cern.ch/geant4-data/releases/source/geant4.10.05.tar.gz
RUN    tar xvf geant4.10.05.tar.gz
WORKDIR    $VIRTUAL_ENV/src/geant4.10.05-build
RUN    cmake -DCMAKE_INSTALL_PREFIX=$VIRTUAL_ENV  -DGEANT4_INSTALL_DATA=ON -DGEANT4_USE_QT=OFF -DGEANT4_USE_RAYTRACER_X11=OFF -DGEANT4_USE_SYSTEM_EXPAT=OFF  -DGEANT4_USE_OPENGL_X11=ON  ../geant4.10.05
RUN    make -j8 install
RUN    echo "source $VIRTUAL_ENV/bin/geant4.sh" >> $VIRTUAL_ENV/bin/activate

# Make port 80 available to the world outside this container
#EXPOSE 80

# Define environment variable
#ENV NAME World

# Run app.py when the container launches
#CMD ["python", "app.py"]
