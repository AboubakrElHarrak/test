![](attachments/banner.png)

# Data Structure Course Design Topic 2 - Realization of Campus Navigation Map

## 1. Design content and requirements

1. Design a school campus floor plan with no less than 30 selected nodes. The vertices in the figure represent various scenic spots on the campus, and store information such as the name, code, and introduction of the scenic spots; the edges represent paths, and store relevant information such as path length.
2. Provide customers with information inquiries about any scenic spot in the picture.
3. Provide customers with directions for any scenic spot in the picture, that is, the shortest path between any two scenic spots, and give the specific path and path length.
4. In addition to completing basic functions, it is best to provide real-time real-time path query, such as the calculation of the shortest path after a path is temporarily closed (to be implemented in the program).
5. Submit the design report, which includes code, design ideas (flow chart) and screenshots of the implementation interface.

## Two, needs analysis

In the design content and requirements, the focus is on the calculation and display of the shortest path between two points. In addition, in order to better present the results of campus navigation, I plan to design a graphical user interface for displaying the results.

Additionally, an "Administrator Mode" needs to be provided. When the user opens the administrator mode, he has the right to disable some paths.

## 3. Finished product display

![](attachments/campus navigation system main view.png)

### 1. Attraction information query

Spots can be selected in three ways.

- Click on the red dot on the map;
- Select from the "Start" or "End" drop-down box on the right;
- Enter the name of the scenic spot in the drop-down box of "Start" or "End" on the right, and the name of the scenic spot must exist.

After selecting the attraction, the "Introduction" tab on the right will display the **attraction name** and the **description** of the attraction.

![](attachments/attraction information query.png)

### 2. Shortest path query between attractions

After you have selected two scenic spots as the starting point and the ending point, you can click the "Calculate Route" button.

![](attachments/Click the "Calculate Path" button.png)

After that, the program will automatically jump to the "Route" tab and display the distance between the two points.

![](attachments/Jump to "route" and display distance.png)

### 3. Admin mode

Right-click the title bar **Zijin College Logo** to switch to administrator mode (right-click again to switch back to normal mode).

![](attachments/right click Zijin College Logo.png)

After entering the administrator mode, the color of the title bar will change to a striking blue for distinction.

![](attachments/administrator mode interface.png)

At this point, all routes will be displayed. Those marked in green are available, and those marked in red are disabled.

Clicking on a route with the mouse toggles the available status of that route.

![](attachments/toggle route available status.png)

The program will save its state in real time. You don't have to worry about losing your modifications if you accidentally close the program.

## Four, get started quickly

This project uses C# language to write the main program, and uses Python language to write auxiliary scripts.

After you have the source code, it is recommended to open the solution `SchoolNavigator.sln` with Visual Studio 2022.

### 1. Install C# environment dependencies

> [!warning] Note
>
> The following operations are based on the fact that you have installed `.NET Core SDK 6.0` and installed C# related workloads in Visual Studio 2022.
>
> - To install .NET Core SDK 6.0, please go to [Microsoft Official Download](https://dotnet.microsoft.com/zh-cn/download/dotnet/6.0);
> - If you have not installed the C# workload, please use `Win + S` to search `Visual Studio Installer` in the installed application, and then set it in the selection menu after opening. Detailed steps can be found online if necessary.

Please install the following dependencies in the built-in NuGet package management tool of Visual Studio:

- `Newtonsoft.Json`
- `MaterialDesignThemes`

### 2. Install Python environment dependencies

> [!warning] Note
>
> The following operations are based on any Python environment where you have installed `Python 3.6` and above.
>
> - If you need to install Python, please go to the official website to download, it is recommended to install the latest version of Python.

Please open `SchoolNavigator` where the project is located in an IDE or editor that supports Python, such as Visual Studio Code, PyCharm or another Visual Studio window.

You should see two Python files inside the folder, namely:

- `JsonDataScripts.py`
- `certificate_all_paths.py`

In the current folder, create a new Python virtual environment:

```cmd
py -m venv venv
```

> [!important]- This tutorial takes Windows 10/11 system as an example
>
> The activation instructions here will vary depending on the type of **OS** or **console** you are using. If the installation fails, use a search engine to find the solution, making sure you have Python installed correctly.

Then activate the Python virtual environment venv:

```cmd
.\venv\Scripts\Activate.ps1
```

In the virtual environment venv, install the following Python packages:

- `svgpathtools`

That is, using the command:

```cmd
pip install svgpathtools
```

### 3. Start the Python script to generate data

In this project, the C# code requires a data file located at `.\data\graph.json`. This file is generated by `JsonDataScripts.py` using `SchoolMap.svg`. So, first start the Python script to generate the data:

```cmd
py JsonDataScripts.py
```

If you see the output `Success.`, the generation is complete.

At this point, the `graph.json` file should be visible in the `.\SchoolNavigator\data\` directory.

### 4. Start the C# program

Back to Visual Studio, after the dependencies are installed, press `F5` or click "SchoolNavigator" to start in Debug mode.

When the main window pops up, the startup is successful. Please get started.

## 5. Outline Design

Outline design involves early technical learning and testing. In the finalized architecture, the following core technical points are covered:

- GUI
- Data acquisition and data processing (emphasis)
- Implementation of the shortest path algorithm (emphasis)
- Display route and node information in the user interface
- Admin mode

### 1. Graphical interface design and implementation

#### Technology selection

At the beginning of the project, I planned to write the backend in C++ language and use the Qt framework to make the graphical interface. C++ is my first language, but after some study and evaluation, I found that the development mode of C++ Qt can't help me quickly create a beautiful and simple interface. On the contrary, using C++ will also make me spend more energy on some obscure syntax and unnecessary low-level implementation.

Therefore, I finally chose the C# language for development, and the front end uses WPF with a relatively mature system for development.

#### interface design

In the interface design part, the first is a large map of Zijin College on the left. The right side is the main function area, which undertakes all the functions of selecting attractions, displaying information of attractions and displaying the distance between attractions.

![campus navigation system main view](attachments/campus navigation system main view.png)

In order to optimize the user experience, I also made the punctuation of the attractions displayed on the map clickable. Users can choose a variety of ways to input the start point and end point.

#### Dynamic display of attraction information

Besides that, I tried to make the user experience and interaction experience as friendly and intuitive as possible. for example:

- When selecting the start point and end point, the detailed information of the scenic spot will be displayed automatically;
- When you click to calculate the route, it will automatically jump to the "Route" tab and display the distance between two points.

### 2. Obtain information from the map: data collection and data processing

Points on the map don't just appear out of thin air. Especially for navigation systems that require a graphical interface, it is necessary to accurately describe the location of each node.

#### Import nodes and paths from the artboard

The first step is to pull a large map covering the entire school from software such as Gaode Map and Baidu Map. First trace all the routes in Adobe Illustrator as a path; at the same time, trace each intersection as a node.

After exporting the contents of the artboard in `.svg` format, use algorithms to extract key information of paths and nodes.

#### Use scripts to extract data and convert it into object information

Program in Python, write the path information provided in `.svg` format: path start point, end point, way point; and node information: coordinate x, coordinate y, and write it into the `.json` file according to your own conventions for the library table.

#### Read in all data at final program startup

When the program is initialized, all the data in the `.json` file is read and stored in memory.

So far, the collection and processing of map information has been completed. We've got:

- All nodes: including coordinate x, coordinate y;
- All routes: including starting point, ending point, en route, distance.

### 3. Implementation of the shortest path algorithm: an improvement on Dijkstra's algorithm

#### Get the shortest path and path nodes at the same time

There are two types of traditional Dijkstra return values, either returning the shortest path or returning the predecessor node. And in my program, because the specific path needs to be displayed in the user interface, that is to say, the path is stored in memory in the form of an object. This requires that my program must be able to find the path through the predecessor node.

#### Load the route distance in advance in the program

According to the above, the coordinates and distance information of the route are stored in the local `.json` file, and are read into the memory during initial loading. The Dijkstra algorithm needs a two-dimensional matrix with the node index as the horizontal and vertical coordinates. The value of a coordinate is the distance.

For example, the value of point `a[1][2]` represents the distance from the point with index `1` to the point with index `2` is `a[1][2]`.

If this part of data is stored in the hard disk, not to mention the need to generate this data through Python scripts in the early stage, the speed of reading data from the hard disk is far slower than reading it directly from the memory. Therefore, the solution I adopted in the project is to generate this matrix in memory after initializing and reading the data file.

### 4. Display route and node information in the user interface

The focus here is to make the user experience more natural and smooth. And after pressing the button, there should be a corresponding event to respond.
By implementing some `Handler` methods by myself and using some `static` variables, I implemented an observer mode delegation system in disguise.

The display of detailed information and the front-end design of buttons, input boxes, etc. are some common knowledge, so I won’t expand them here.

### 5. Admin Mode: Disable routes

Get the extra ability to disable routes when admin mode is enabled. Obviously, this is also achieved by setting a `static` variable as a flag.

At the same time, when exiting the administrator mode, the set content will be saved.

## Six, detailed design

In the detailed design, the key points mentioned in the summary design will be explained, and the key parts will be explained with code assistance.

### 1. Database table design

- path `Path`:

| name | field | type | primary key? | Can be empty? |
| ------------ | ---------------- | --- | ------ | ------ |
| index | `Id` | `int` | is | |
| name | `Name` | `string` | | is |
| length | `Distance` | `double` | | |
| Starting point ID | `StartVerticeId` | `int` | | |
| Endpoint ID | `EndVerticeId` | `int` | | |
| Front-end data description | `Data` | `string
