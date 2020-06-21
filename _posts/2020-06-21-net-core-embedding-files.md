---
layout: post
title: "Embedding and Reading Back Files in C#"
tags: blog code
description: Going over how to embed a standard file into an application's exe or dll file and read the data back at runtime.
---

## Using Embedded Files in C# (.Net Core/.Net 5)
An issue I stumbled across recently while working on a desktop application was needing to store license data for a certain library I would be depending on. The license needed to be put into the library at runtime, and this was causing me issues with how to actually store it in the application. I couldn't hard-code it anywhere as the app was to be open source, and missing a class would cause others to have issues building. I also couldn't read an external file as the end deployment needed to be a single file application that could be placed anywhere and run. Registry and other "install-time" storages were out as I wanted it to survive OS reinstalls (especially as I am prone to wiping my system as soon as main drive storage gets to much for my comfortability).

Enter an interesting part of .Net, embedded files. With this we can take any sort of data file, put it into our application, and using a little bit of reflection, read it back at runtime. The first step is to put your desired files into the project. For this example, we will use `Newtonsoft.Json` to read back a simple json file. Set the build action of the file or files to be "EmbeddedResource", you should have something like this in the main section of your `.csproj` file when done. You do not need to set "Copy to Output Directory"
```xml
<ItemGroup>
  <None Remove="MyFile.json" />
  <EmbeddedResource Include="MyFile.json" />
</ItemGroup>
```

Now we have a file being embedded into our app. Next step is to load and work with the data at runtime. To start we need a reference to the assembly the data is being stored in. This is likely to be your "Executing Assembly" unless you are working with multiple projects. Next we create a resource stream from it and pass the path to our file. Lastly we work with the stream and can read the data out to work with it as we need.
```cs
// A reference to the assembly the data is stored in
var assembly = Assembly.GetExecutingAssembly();
// Load our resource stream. Replace "Namespace" with your project's root namespace
using var stream = assembly.GetManifestResourceStream("Namespace.MyFile.json");
// Create a disposable reader of our stream, note stream may be null if the file wasn't found above
using var reader = new StreamReader(stream);
// Finally read out our text and make a C# object from it
var textData = reader.ReadToEnd();
JsonConvert.DeserializeObject<MyFileData>(textData);
```
