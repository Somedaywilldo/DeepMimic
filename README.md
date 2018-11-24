# DeepMimic Environments Setup

Somedaywilldo

## C Libraries

### OpenGL (This includes freeglut and glew)

```bash
$ sudo apt-get install cmake libx11-dev xorg-dev libglu1-mesa-dev freeglut3-dev libglew1.5 libglew1.5-dev libglu1-mesa 
```

### Eigen (Build on source)

```bash
$ git clone https://github.com/eigenteam/eigen-git-mirror
$ mkdir build
$ cmake ../
$ sudo make install
```

### Bullet

```bash
$ git clone https://github.com/bulletphysics/bullet3
$ cd bullet3
$ vi build_cmake_pybullet_double.sh
```

This is important, in **build_cmake_pybullet_double.sh**, change line 8  "-DUSE_DOUBLE_PRECISION=ON" to " -DUSE_DOUBLE_PRECISION=OFF" like following:

```bash
$ cmake -DBUILD_PYBULLET=ON -DBUILD_PYBULLET_NUMPY=ON -DUSE_DOUBLE_PRECISION=OFF -DBT_USE_EGL=ON -DCMAKE_BUILD_TYPE=Release .. || exit 1
```

Then run these:

```bash
$ sh build_cmake_pybullet_double.sh
$ cd build_cmake
$ sudo make install
```

### Swig and MPI

```bash
$ sudo apt-get install swig
$ sudo apt install libopenmpi-dev
```

### Clang

```bash
$ sudo apt-get install clang
```

I don't know if g++ also works, it's just weird to use clang in Ubuntu. 

## Python Libraries

Notice: C libraries must be installed first. All in Python3. Tensorflow-gpu is not required.

Just go to the **DeepMimic/** :

```bash
$ sudo pip install -r requirements.txt
```



## Hacks (IMPORTANT)

Before building DeepMimic, we need adjust **~/.bashrc** and **Makefile** first.

### ~/.bashrc

Some paths maybe useless, just in case. 

```bash
LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/lib"
LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/lib" #My eigen and bullet is here
PYTHONPATH="$PYTHONPATH:/usr/include/python3.5m"
PYTHONPATH="$PYTHONPATH:/usr/local/lib"
```

The **LD_LIBRARY_PATH** is for this error:

```
ImportError: libBulletDynamics.so.2.88 ..........
```

### Makefile 

```bash
  6 LOCAL_DIR = /usr/local # modified: added for my bullet and eigen lib
  7
  8 EIGEN_DIR = /usr/local/include/eigen3
  9 BULLET_INC_DIR = /usr/local/include/bullet
 10
 11 PYTHON_INC = /usr/include/python3.5m
 12 PYTHON_LIB = /usr/lib/ -lpython3.5m
 13
 14 INC = -I./ \
 15     -I$(LOCAL_DIR) \ # modified
 16     -I$(EIGEN_DIR) \
 17     -I$(BULLET_INC_DIR)
 18
 19 LIBS = -lGLEW -lGL -lGLU -lglut -lBulletDynamics -lBulletCollision -lLi    nearMath -lm -lstdc++
```



### Build and Run

Now build the DeepMimic: (for life is short, add an -j8 to make)

```bash
$ cd DeepMimic
$ cd DeepMimicCore
$ make -j8 python 
```

Then these should be runnable, inside **mpi_run.py**, some line may need to be changed (modify python to python3)

```bash
$ python3 DeepMimic.py --arg_file args/run_humanoid3d_spinkick_args.txt
$ python3 DeepMimic.py --arg_file args/kin_char_args.txt
$ python3 mpi_run.py --arg_file args/train_humanoid3d_spinkick_args.txt --num_workers 4
```

And if these don't work, I have no idea : )

