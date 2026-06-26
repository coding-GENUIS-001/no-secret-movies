# Build Instructions for No Secret Movies

## System Requirements

### Linux/macOS
- GCC/Clang compiler with C++17 support
- CMake 3.15+
- SQLite3 development libraries
- OpenSSL development libraries
- Git

### Windows
- Visual Studio 2019+ or MinGW with C++17 support
- CMake 3.15+
- SQLite3
- OpenSSL
- Git

## Installation Steps

### Linux (Ubuntu/Debian)

```bash
# 1. Install dependencies
sudo apt-get update
sudo apt-get install -y \
    build-essential \
    cmake \
    git \
    libsqlite3-dev \
    libssl-dev \
    libssl1.1

# 2. Clone the repository
git clone https://github.com/coding-GENUIS-001/no-secret-movies.git
cd no-secret-movies

# 3. Create build directory
mkdir build
cd build

# 4. Configure with CMake
cmake ..

# 5. Build the project
make

# 6. Run the application
./no-secret-movies
```

### macOS

```bash
# 1. Install Homebrew (if not already installed)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 2. Install dependencies
brew install cmake sqlite3 openssl

# 3. Clone the repository
git clone https://github.com/coding-GENUIS-001/no-secret-movies.git
cd no-secret-movies

# 4. Create build directory
mkdir build
cd build

# 5. Configure with CMake
cmake ..

# 6. Build the project
make

# 7. Run the application
./no-secret-movies
```

### Windows (with Visual Studio)

```cmd
REM 1. Install Visual Studio Build Tools with C++17 support

REM 2. Clone the repository
git clone https://github.com/coding-GENUIS-001/no-secret-movies.git
cd no-secret-movies

REM 3. Create build directory
mkdir build
cd build

REM 4. Configure with CMake
cmake .. -G "Visual Studio 16 2019"

REM 5. Build the project
cmake --build . --config Release

REM 6. Run the application
.\Release\no-secret-movies.exe
```

## Docker Installation

```bash
# 1. Clone the repository
git clone https://github.com/coding-GENUIS-001/no-secret-movies.git
cd no-secret-movies

# 2. Build Docker image
docker build -t no-secret-movies:latest .

# 3. Run the container
docker run -d \
    -p 8080:8080 \
    --name no-secret-movies \
    no-secret-movies:latest

# 4. View logs
docker logs -f no-secret-movies

# 5. Stop the container
docker stop no-secret-movies
```

## Accessing the Application

After building and running:

1. **Open your browser** and navigate to: `http://localhost:8080`
2. **Homepage** - Browse movies, search, filter by genre
3. **Login/Register** - Create an account
4. **Watch Movies** - Click "Watch Now" on any movie
5. **API Access** - Direct API calls available at `/api/*`

## Troubleshooting

### CMake can't find SQLite3

**Linux:**
```bash
sudo apt-get install libsqlite3-dev
cmake -DSQLITE3_INCLUDE_DIR=/usr/include -DSQLITE3_LIBRARY=/usr/lib/x86_64-linux-gnu/libsqlite3.so ..
```

**macOS:**
```bash
brew install sqlite3
cmake -DSQLITE3_INCLUDE_DIR=$(brew --prefix sqlite3)/include -DSQLITE3_LIBRARY=$(brew --prefix sqlite3)/lib/libsqlite3.dylib ..
```

### Permission Denied on Linux

```bash
chmod +x ./no-secret-movies
./no-secret-movies
```

### Port Already in Use

The default port is 8080. To use a different port, modify the code in `src/main.cpp`:

```cpp
HTTPServer server(9000, "0.0.0.0");  // Change 8080 to 9000
```

## Build Options

### Debug Build
```bash
cmake -DCMAKE_BUILD_TYPE=Debug ..
make
```

### Release Build (optimized)
```bash
cmake -DCMAKE_BUILD_TYPE=Release ..
make
```

### Verbose Output
```bash
cmake ..
make VERBOSE=1
```

## Development

### Code Structure
- **src/** - Implementation files
- **include/** - Header files
- **public/** - Frontend files (HTML, CSS, JS)
- **CMakeLists.txt** - Build configuration

### Modifying the Code

1. Edit source files in `src/` or `include/`
2. Rebuild: `cmake --build . --clean-first`
3. Run: `./no-secret-movies`

### Adding New Dependencies

Edit `CMakeLists.txt`:
```cmake
find_package(NewLibrary REQUIRED)
target_link_libraries(no-secret-movies NewLibrary::NewLibrary)
```

## Running Tests

```bash
cd build
ctest --output-on-failure
```

## Clean Build

```bash
cd build
rm -rf *
cmake ..
make
```

## Performance Tips

- Use **Release build** for production
- Build with optimization: `-O3`
- Use multiple cores: `make -j$(nproc)`

## Support

For issues or questions:
1. Check the README.md
2. Review ARCHITECTURE.md for system design
3. Open an issue on GitHub
4. Contact: support@nosecretmovies.com

---

**Happy Building! 🎬**
