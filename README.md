# Memory Class - Process Memory Management Library

A comprehensive C# library for reading and writing memory of running processes on Windows. This library is designed for game development, debugging, reverse engineering, and system analysis applications.

## 🚀 Features

- **Process Management**: Find and attach to target processes
- **Memory Read/Write**: Read and write different data types from/to memory
- **Pattern Scanning**: Search for specific byte patterns in memory
- **World-to-Screen Conversion**: Transform 3D coordinates to 2D screen coordinates
- **Module Detection**: Find and get base addresses of loaded modules
- **Matrix Operations**: Read matrix data from memory
- **String Operations**: Read strings with custom encoding support

## 📋 Requirements

- .NET Framework or .NET Core
- Windows operating system
- Administrative privileges (for some operations)

## 💻 Usage Examples

### Process Initialization

```csharp
// Initialize connection to a process
var result = Memory.Initalize("notepad");
if (result == Memory.Enums.InitalizeResult.OK)
{
    Console.WriteLine($"Successfully attached to {Memory.Name} (PID: {Memory.Id})");
}
else
{
    Console.WriteLine($"Failed to attach: {result}");
}
```

### Reading Memory

```csharp
// Read different data types
int intValue = Memory.Read<int>(0x12345678);
float floatValue = Memory.Read<float>(0x12345678);
byte[] byteArray = Memory.ReadBytes(0x12345678, 10);

// Read with DMA (pointer chains)
int[] offsets = { 0x10, 0x20, 0x30 };
int finalValue = Memory.Read<int>(0x12345678, offsets);

// Read strings
string text = Memory.ReadString(0x12345678, 50, Encoding.UTF8);
```

### Writing Memory

```csharp
// Write different data types
Memory.Write<int>(0x12345678, 100);
Memory.Write<float>(0x12345678, 3.14f);

// Write with DMA
int[] offsets = { 0x10, 0x20, 0x30 };
Memory.Write<int>(0x12345678, offsets, 500);
```

### Pattern Scanning

```csharp
// Find pattern in module
string pattern = "48 89 5C 24 ? 48 89 6C 24 ? 48 89 74 24";
int address = Memory.ScanPattern("game.exe", pattern);

if (address != -1)
{
    Console.WriteLine($"Pattern found at: 0x{address:X}");
}

// Advanced pattern scanning with offset
int advancedAddress = Memory.ScanPattern("game.exe", pattern, 4, 0, true);
```

### World-to-Screen Conversion

```csharp
// Convert 3D world coordinates to 2D screen coordinates
Vector3 worldPosition = new Vector3(100, 200, 300);
float[] viewMatrix = new float[16]; // Your view matrix
int screenWidth = 1920;
int screenHeight = 1080;

if (Memory.WorldToScreen(worldPosition, out Vector2 screenPos, viewMatrix, screenWidth, screenHeight))
{
    Console.WriteLine($"Screen position: X={screenPos.X}, Y={screenPos.Y}");
}
```

### Module Management

```csharp
// Get module base address
int moduleAddress = Memory.GetModuleAddress("kernel32.dll");
if (moduleAddress != -1)
{
    Console.WriteLine($"Module base address: 0x{moduleAddress:X}");
}
```

### Reading Matrices

```csharp
// Read a 4x4 matrix (16 floats)
float[] matrix = Memory.ReadMatrix<float>(0x12345678, 16);
```

## 🏗️ Class Structure

### Main Properties
- `TargetProcess`: The target process object
- `Name`: Process name
- `Id`: Process ID
- `Handle`: Process handle
- `BaseAddress`: Base address of the main module

### Initialization Methods
- `Initalize(string ProcessName)`: Basic process initialization
- `Initalize(string ProcessName, ProcessAccessFlags ExtraFlags)`: Initialization with custom flags
- `Restore()`: Clean up and close handles

### Memory Operations
- `ReadMemory(int address, int size)`: Read raw bytes
- `ReadBytes(int address, int size)`: Read byte array
- `Read<T>(int address)`: Read typed data
- `Write<T>(int address, object value)`: Write typed data
- `FindDMAAddy(IntPtr ptr, int[] offsets)`: Follow pointer chains

### Advanced Features
- `ScanPattern(string module_name, string signature)`: Pattern scanning
- `WorldToScreen(Vector3 target, out Vector2 pos, float[] viewmatrix, int width, int height)`: 3D to 2D conversion
- `GetModuleAddress(string module_name)`: Get module base address

## 🔒 Security Considerations

- This library requires administrative privileges for most operations
- Use responsibly and only on processes you own or have permission to access
- Be aware of anti-cheat systems when working with games
- Always properly dispose of handles using the `Restore()` method

## ⚠️ Error Handling

The library includes comprehensive error handling:

```csharp
public enum InitalizeResult
{
    OK,                        // Success
    PROCESS_NOT_FOUND,         // Target process not found
    ACCES_VIOLATION_ERROR      // Access denied or other error
}
```

## 🎮 Graphics API Support

The WorldToScreen function supports both DirectX and OpenGL coordinate systems:

```csharp
// DirectX (default)
Memory.WorldToScreen(worldPos, out screenPos, viewMatrix, width, height, Memory.Enums.GraphicAPI.DirectX);

// OpenGL
Memory.WorldToScreen(worldPos, out screenPos, viewMatrix, width, height, Memory.Enums.GraphicAPI.OpenGL);
```

## 🛠️ Troubleshooting

### Common Issues

1. **Access Denied**: Run your application as administrator
2. **Process Not Found**: Ensure the target process is running and the name is correct
3. **Invalid Memory Address**: Check if the address is valid and accessible
4. **Pattern Not Found**: Verify the pattern syntax and ensure it exists in the target module

### Best Practices

- Always check initialization results before proceeding
- Use try-catch blocks around memory operations
- Call `Restore()` when finished to clean up resources
- Test patterns in a hex editor before using them in code

## 📝 License

This code is provided as-is for educational and research purposes. Use responsibly and in accordance with your local laws and the terms of service of any applications you interact with.

## 🤝 Contributing

Feel free to submit issues, feature requests, or pull requests to improve this library.
