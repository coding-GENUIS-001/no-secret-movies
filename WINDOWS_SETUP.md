# Windows Installation Guide for No Secret Movies

## 🪟 Windows Setup - Step by Step

### **Step 1: Install Required Tools**

#### A. Install Git
1. Go to: https://git-scm.com/download/win
2. Download the installer
3. Run it and click "Next" through all prompts
4. Keep all defaults selected

#### B. Install CMake
1. Go to: https://cmake.org/download/
2. Download "Windows x64 Installer"
3. Run the installer
4. **IMPORTANT:** When asked "Add CMake to PATH", select **"Add CMake to the system PATH for all users"**
5. Click "Install"

#### C. Install Visual Studio Build Tools (C++ Compiler)
1. Go to: https://visualstudio.microsoft.com/downloads/
2. Scroll down and find "Tools for Visual Studio"
3. Download "Visual Studio Build Tools 2022"
4. Run the installer
5. When prompted to select workloads, check:
   - ✅ "Desktop development with C++"
6. Click "Install"
7. Wait for installation (takes 10-15 minutes)
8. Restart your computer when done

#### D. Install OpenSSL
1. Go to: https://slproweb.com/products/Win32OpenSSL.html
2. Download "Win64 OpenSSL v3.x.x" (the full, not "Light")
3. Run the installer
4. Click "Next" through prompts
5. When asked for destination, choose: `C:\Program Files\OpenSSL-Win64`
6. Click "Install"

#### E. Install SQLite3
1. Go to: https://www.sqlite.org/download.html
2. Download "sqlite-tools-win32-x86-3440000.zip" (or latest version)
3. Extract it to: `C:\sqlite`
4. Open a new Command Prompt and verify:
   ```cmd
   C:\sqlite\sqlite3 --version
   ```
   You should see a version number

---

### **Step 2: Clone Your Project**

1. Open **Command Prompt** (press `Win+R`, type `cmd`, press Enter)
2. Navigate to a good location (like Documents or Desktop):
   ```cmd
   cd C:\Users\YourUsername\Desktop
   ```
3. Clone the repository:
   ```cmd
   git clone https://github.com/coding-GENUIS-001/no-secret-movies.git
   ```
4. Enter the directory:
   ```cmd
   cd no-secret-movies
   ```

---

### **Step 3: Build the Project**

1. Create build directory:
   ```cmd
   mkdir build
   cd build
   ```

2. Configure with CMake:
   ```cmd
   cmake .. -G "Visual Studio 17 2022"
   ```
   
   If that doesn't work, try:
   ```cmd
   cmake .. -G "Visual Studio 16 2019"
   ```

3. Build the project:
   ```cmd
   cmake --build . --config Release
   ```
   
   This will take 5-10 minutes. You should see lots of compilation messages.

4. Check if build succeeded:
   ```cmd
   dir Release\
   ```
   
   You should see `no-secret-movies.exe` in the output

---

### **Step 4: Run the Server**

1. Run the executable:
   ```cmd
   .\Release\no-secret-movies.exe
   ```

2. You should see output like:
   ```
   🎬 No Secret Movies - C++ Movie Website
   ========================================
   
   [*] Initializing database at: no_secret_movies.db
   [+] Database initialized successfully
   [+] Authentication initialized
   [+] HTTP Server started on 0.0.0.0:8080
   [+] Server is running!
   [+] Access the website at http://localhost:8080
   [+] Press Ctrl+C to stop the server
   ```

3. **Open your web browser** and go to: `http://localhost:8080`

---

## 🎉 You Should Now See:

✅ **Homepage** with featured movies
✅ **Search bar** to find movies
✅ **Genre filters** (Action, Sci-Fi, Crime, etc.)
✅ **Movie cards** with titles and ratings
✅ **Login/Register buttons**

---

## 🧪 Test the Features

Once the website loads:

| Feature | How to Test |
|---------|------------|
| **Browse Movies** | Scroll down to see 5 featured movies |
| **Search** | Type "Matrix" in search box, click Search |
| **Filter by Genre** | Click "Sci-Fi" button |
| **View Details** | Click on any movie card |
| **Login** | Click "Login" (test data) |
| **Register** | Click "Register" to create account |

---

## ⚠️ Windows Troubleshooting

### Problem: "cmake is not recognized"
**Solution:** 
- Uninstall CMake
- Reinstall it and make sure to check "Add CMake to PATH"
- Restart Command Prompt after installation

### Problem: "Visual Studio compiler not found"
**Solution:**
- Make sure Build Tools for Visual Studio are installed
- Restart Command Prompt
- Try the cmake command again

### Problem: "sqlite3.h not found"
**Solution:**
1. Create `C:\sqlite\include` folder
2. Download `sqlite3.h` from https://www.sqlite.org/download.html
3. Put it in `C:\sqlite\include`
4. Run cmake with this command:
   ```cmd
   cmake .. -G "Visual Studio 17 2022" -DSQLITE3_INCLUDE_DIR=C:\sqlite\include -DSQLITE3_LIBRARY=C:\sqlite\sqlite3.lib
   ```

### Problem: "Port 8080 already in use"
**Solution:** Kill the process using port 8080:
```cmd
netstat -ano | findstr :8080
taskkill /PID <PID> /F
```

### Problem: Build fails with many errors
**Solution:**
1. Delete the build folder: `rmdir /s build`
2. Create new build folder: `mkdir build && cd build`
3. Try again with explicit paths:
   ```cmd
   cmake .. -G "Visual Studio 17 2022" -DCMAKE_PREFIX_PATH="C:\Program Files\OpenSSL-Win64"
   cmake --build . --config Release
   ```

---

## 🚀 After First Run

The first time you run it, the database will be created with 5 sample movies:
- The Matrix
- Inception
- The Dark Knight
- Interstellar
- Pulp Fiction

To stop the server: Press **Ctrl+C** in the Command Prompt

---

## 📝 Quick Reference Commands

```cmd
REM Navigate to project
cd C:\Users\YourUsername\Desktop\no-secret-movies

REM Enter build directory
cd build

REM Configure (do this once)
cmake .. -G "Visual Studio 17 2022"

REM Build
cmake --build . --config Release

REM Run
.\Release\no-secret-movies.exe

REM Clean and rebuild
rmdir /s build
mkdir build
cd build
cmake .. -G "Visual Studio 17 2022"
cmake --build . --config Release
.\Release\no-secret-movies.exe
```

---

## 🐳 Alternative: Use Docker (MUCH EASIER!)

If all of this seems complicated, you can use Docker instead:

1. Install Docker Desktop: https://www.docker.com/products/docker-desktop
2. Open Command Prompt:
   ```cmd
   cd no-secret-movies
   docker-compose up
   ```
3. Open browser: `http://localhost:8080`

Done! No compilation needed! 🎉

---

## 📞 Need Help?

1. **Copy the error message** from Command Prompt
2. **Tell me what went wrong**
3. I'll help you fix it!

---

**You've got this! 💪 Let me know if you hit any issues!**
