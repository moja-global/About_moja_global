

# **Metric and carbon calculation modules**

Author: [Shubham K](https://github.com/shubhamkarande13)

# Table of contents

- [**Metric and carbon calculation modules**](#metric-and-carbon-calculation-modules)
- [Table of contents](#table-of-contents)
- [**Goal of this document**](#goal-of-this-document)
- [**Glossary**](#glossary)
- [**Background**](#background)
  - [**_What are modules?_**](#what-are-modules)
  - [**_What does a Module do?_**](#what-does-a-module-do)
  - [**_Types of FLINT modules:_**](#types-of-flint-modules)
  - [**_What are Operations?_**](#what-are-operations)
  - [**_What are Pools?_**](#what-are-pools)
  - [**_What are Events?_**](#what-are-events)
  - [**_What kinds of modules are there?_**](#what-kinds-of-modules-are-there)
  - [**_Why are modules important?_**](#why-are-modules-important)
  - [**_How do we import a module?_**](#how-do-we-import-a-module)
  - [**_How are modules configured during a FLINT run?_**](#how-are-modules-configured-during-a-flint-run)
  - [**_How do modules interact?_**](#how-do-modules-interact)
- [**Developing modules**](#developing-modules)
  - [**_What are the typical components of a module?_**](#what-are-the-typical-components-of-a-module)
  - [**_How can existing modules be combined?_**](#how-can-existing-modules-be-combined)
  - [**_How do I create a new module?_**](#how-do-i-create-a-new-module)
  - [**_Choosing a language_**](#choosing-a-language)
    - [**_Translating a system of equations into a new module_**](#translating-a-system-of-equations-into-a-new-module)
    - [**_Wrapping existing models_**](#wrapping-existing-models)
- [**Creating and adding a module to FLINT**](#creating-and-adding-a-module-to-flint)
  - [**libraryfactory.h**](#libraryfactoryh)
  - [**libraryfactory.cpp**](#libraryfactorycpp)
  - [**testmodule.h**](#testmoduleh)
  - [**testmodule.cpp**](#testmodulecpp)
  - [**Configuration files in FLINT**](#configuration-files-in-flint)
    - [**BuildLandUnit Module**](#buildlandunit-module)
    - [**ErrorScreenWriter Module**](#errorscreenwriter-module)
    - [**SystemSettings**](#systemsettings)
  - [**Disturbance Events**](#disturbance-events)
  - [**_What are Disturbance Events?_**](#what-are-disturbance-events)
    - [**Moving stocks from one pool to another**](#moving-stocks-from-one-pool-to-another)
    - [**Changing fluxes**](#changing-fluxes)
    - [**Change the ecosystem type**](#change-the-ecosystem-type)
    - [**Disturbances in FLINT**](#disturbances-in-flint)
  - [***System Providers in FLINT***](#system-providers-in-flint)
    - [**SQLite Provider**](#sqlite-provider)
- [**Key Components of the FLINT**](#key-components-of-the-flint)


# **Goal of this document**

Demonstrate how to implement a single, simple module from scratch. 

This will include:

*   issues to consider when starting a module
*   the boilerplate for a blank module, 
*   the source and header files that describe the process of the module,
*   the connections and registration of the module to the FLINT engine
*   setting default configuration and test dataset
*   calling the module from the command line

Further extensions will include running the module spatially, passing external data and new configuration, and linking it with other modules, but this may happen at a later date.

# **Glossary** 



*   DynamicVariable & DynamicObject : Derived from Portable Components (POCO) library in dynamic.h. They are used to convert the data from one type to another with minimum or no data loss. More info: [http://bit.ly/3ddglyr](http://bit.ly/3ddglyr)
*   override : C++ keyword used to match the function definition with the module function definition in the module base header file.
*   extern: The **extern** "**C**" keyword tells a C++ compiler not to mangle (rename) **C** function names. It changes the linkage of a function in such a way that the function is callable from **C**. In practice that means that the function name is not mangled.
*   _landUnitData :This is the module’s view of the LandUnitController - it interacts with the land unit controller on behalf of the module to create and submit carbon transfers (createStockOperation/createProportionalOperation/submitOperation) and get references to pools and variables.
*   landUnitController: Each thread gets its own LandUnitController which provides access to pools/variables and carbon transfers for the current pixel.
*   Signals: Modules in the FLINT are event-driven, meaning that to do any work, they need to choose one or more events to subscribe to and provide a handler method for. The sequence of events is found in a couple of different places: the LocalDomainController (i.e. moja.flint.SpatialTiledLocalDomainController), and the sequencer module (i.e. moja.flint.CalendarAndEventSequencer) - see all the calls to _notificationCenter.postNotification. In general, the LocalDomainController deals with system and block-level events, and the Sequencer module deals with the pixel-level time loop - the events fired from there are typically where most of the module work is done.
*   onLocalDomainInit() : This event is fired once per thread (but each thread has its own instance of the module) at the start of the simulation - modules typically subscribe to this event in order to store references to pools and variables using _landUnitData->getPool and _landUnitData->getVariable. At this stage, pools and variables have been initialized, so if a module needs to pre-load some data from a database, for example, it can do that here. There is no “current pixel” during this event, so spatial data is not available.
*   LocalDomain: Basically the current thread in use and its own set of module instances, etc.
*   getPool: Gets a reference to a pool - usually a carbon pool, but a pool is really just a container for a number representing a quantity of something. Most of the work of a module is just transferring amounts between pools. When a module gets a reference to a pool, it is guaranteed by the framework to always point to the current pixel.
*   getVariable: Gets a reference to a variable - variables come in two main categories: transforms, which retrieve read-only data, and read/write variables that contain more “state”-type data. Note that read/write variable values persist across pixels: for example, if a variable “a” starts at 0, and a module sets it to 1 after processing a pixel, the variable’s value will still be 1 after moving on to the next pixel. Pool values are different in that they get reset to their starting/default value after every pixel.
*   addTransfer: After creating an operation (proportional or absolute), which tells the FLINT that the associated transfers are either proportions or specific amounts, we need to add one or more transfers to the operation before submitting it. A transfer is a movement of units (carbon or whatever the pool represents) from one pool to another. For example: _landUnitData->createProportionalOperation(), then addTransfer(softwood, deadwood, 0.5) would transfer half of the amount in the softwood pool to the deadwood pool. \
Transfers within the same operation occur simultaneously - that is, if the same operation had two transfers instead: softwood -> deadwood @ 0.5, softwood -> soil @ 0.5, half of the softwood would go to deadwood and half would go to soil and none would remain.
*   submitOperation: Submits an operation (set of pool transfers) to the system. All transfers are tracked by the system so that an output module can read them and write the transfer information to a database or other repository.
*   Datarepository: Moja.datarepository contains various types of data providers that read from spatial layers, databases, etc. and make that data available through the various transform classes, and finally through named variables that modules read from. One example would be an ExternalVariable with a LocationIdxFromFlintDataTransform using a TileRasterReaderGDAL provider to read a data value for the current pixel from a spatial layer.
*   The _modules.base_exports.h file in header files/other: This file is generated by CMake and, when CMake is properly configured for a new module, defines the &lt;some module>_API macro that needs to go in front of any classes we want to make visible to the FLINT, i.e. class KOREA_API KoreaGrowthCurveTransform : public flint::ITransform. Without the KOREA_API macro in front, the FLINT won’t be able to use that class from the module dll.


# **Background**


## **_What are modules?_**

Modules are the building block of FLINT. These contain the operations, describing the ecological processes and driving the carbon changes in the landscape.

There are multiple types of modules in FLINTpro to perform different functions.For the sake of simplicity, we use the term module in this document to mean calculation modules. Other modules are covered in other documents. 

This document describes how to create FLINT modules for calculating metrics such as carbon and GHG emissions. These modules can come in all different forms. 

All operations, processes and events are managed through modules. A module is a self-contained set of operations that determine the state of, or change in, variables across a specified period of time for a single Simulation Unit in direct response to notifications from the FLINT core system (Unit Controller). Notifications can be triggered from events, or time-steps. For example, the empirical forest growth module includes all the operations required to simulate biomass accumulation in forests.

Each module reads (or is provided with) information about the current state variables and the data required to update the state variables, such as climate data or information about events to simulate. Each module then performs the required calculations and returns information about the updates to apply to each of the state variables and carbon pools. For state variables, such as age, it is possible for modules to return the updated value, but for all carbon pools and other fluxes the module returns an array (sparse matrix) of the proposed operations. This array will include the information about the source pool, the sink pool, and the amount ( tC ha-1 per time step) of the flux. Module-specific metadata regarding units and time step size are also required. This information is all returned to the Unit Controller.


## **_What does a Module do?_**

Modules connect with FLINT resources (Pools, Variables, Timing). While there isn’t always a clear distinction between the two, there are generally two types of Modules - Calculation Modules, and Functionality Modules. 


## **_Types of FLINT modules:_**

1. **Calculation Modules** use operations to change the state of Variables (e.g. tree age, “10”, etc.) and Pools (e.g. Aboveground biomass, Belowground biomass etc.) for a simulation unit (pixel), over a sequence of steps. The Module doesn’t include the concept of location or sequence of events, simply using available information (Variables, Pools, Stored state information) to perform specific calculations (e.g. required carbon movements), and sets the new state of Variables and Pools to the FLINT.  \
Calculation modules can be geographically flexible  (e.g. a global default value such as Tier 1 Forests), or geographically restrictive (Kenya-specific SLEEK Forests), depending on the original design of the module. Flexible modules are constructed to use generic or global datasets, allowing the modules to be readily applied in different areas. Whereas other modules are constructed to use a specific dataset, which creates a simpler, geographically-specific module, but significantly reduces the flexibility of its application. To date, all calculation modules are proprietary, with rights with the Kenyan Government, the Canadian Forest Service, or Mullion Group.  
2. **Functionality Modules** affect the utility of the FLINT, but generally don’t make any changes to variables or Pools. This can be a utility for managing outputs, or performance of the system. This can include managing output databases, error logging, developing spatial outputs,  or distributed processing. For example, the aggregator module will aggregate fluxes to geographic areas.


## **_What are Operations?_**

An operation is a process within the FLINT that moves carbon stock between pools.  Operations are defined within modules, and can reflect _processes_, such as growth, or _events _such as harvests, or fires_,_ whether natural or human induced. For example, an operation reflecting a harvest event, moves plant material to products and debris pools, while a wildfire moves plant material to debris and atmospheric pools. The amount of stock moved during an operation is referred to as a ‘flux’. Operations allow the FLINT to track changes in carbon stock through time, including fluxes into and out of pools.

FLINT uses operations to update values for Simulation Units and to record flux values in a flux table. For example, an operation reflecting plant growth can be applied to aboveground biomass pools to estimate the growth flux over time. FLINT has been designed to ensure the conservation of mass (and area) throughout the calculation process. This means that FLINT will only transfer carbon stock from one pool to another through known and valid pathways, ensuring the system is balanced so that the sum of all the fluxes is equal to the sum of the stock changes.


## **_What are Pools?_**

A pool is a reservoir within which something can be stored. The most obvious example is a carbon pool which is a reservoir into which carbon can be stored. Within FLINT, each pool is attributed a value, for example tonnes of carbon, and at each time step the FLINT can move stores from one pool to another using operations (discussed below). The fluxes between pools, including  the atmosphere, are used to calculate changes in carbon stock and resulting emissions and removals. 

Pools are fully configurable depending on the modules used or data available (e.g. harvested wood products). Since the emissions estimates will be reported to the UNFCCC, all carbon pools will need to be aggregated into the IPCC pool types (aboveground biomass, belowground biomass, deadwood, litter, soil (mineral and organic)) before they are reported.


## **_What are Events?_**

Events are operations that occur intermittently (rather than for every time step in a simulation) resulting in the movement of carbon from one pool to another. Events include natural and anthropogenic events including fire, harvesting, ploughing, and fertiliser application. Events are coded for the FLINT as a module.


## **_What kinds of modules are there?_**

*   Point process
*   Spatially explicit
*   Unlinked - driven by input data
*   Linked - driven by multiple modules and associated parameters


## **_Why are modules important?_**

Without calculation modules, the FLINT cannot produce outputs. Each user will have different data and policy and reporting needs. Based on these needs users will attach the appropriate modules and data to the FLINT. 

Given the wide range of multiple 


## **_How do we import a module?_**

Two common places to find modules:

*   Modules in main FLINT repo
*   Custom modules, developed by users

The second half of this document describes creating a custom module.


## **_How are modules configured during a FLINT run?_**
Modules are configured with the help of config files in JSON format. 


## **_How do modules interact?_**

This might be relevant: [https://github.com/moja-global/About_moja_global/issues/66](https://github.com/moja-global/About_moja_global/issues/66)


# **Developing modules**


## **_What are the typical components of a module?_**

There are three main components of a module

Input data: Data inputs for accurate calculations.

Algorithms: The equations that are used to calculate the stocks and flows of the metric.

Output data: Data results handed back to the FLINT following the calculation. 


## **_How can existing modules be combined?_**

This should describe configuration and compilation.

Ideally modules are separated to the simplest functions. For example, it is typically desirable to break a complex model down into several smaller modules as this will make modification and changes easier in the future. It also allows for components to be more easily reused across multiple applications of the FLINT.

Combining modules occurs in three main ways:

*   Linking through the stocks/pools being record in the FLINT
*   Setting the order of operation of modules in a time step
    *   That is, what modules need to run in what order for the system to work.
*   Linking all modules to the same core input data and event triggering systems
    *   Under all circumstances, all modules will be subscribed to the same event triggering system to avoid errors of omission and commission.

Modules that act in isolation are those that 1) work on their own pools and stocks (i.e., no other modules interact with the stocks or flows) and 2) do not interact with other modules. A good example of these modules are emissions factor approaches for non-CO2 emissions. For example, N2O emissions from fertiliser application is often calculated simply as a percentage of the amount of N applied by type. They do not consider the N status of the soil or other factors. 

In more advanced systems it is highly likely that modules will interact in at least some way. For mass-balance type methods multiple modules will operate on the same set of pools. For example, a **_dead organic matter pool _**(DOM) will have carbon added from modules that calculate plant turnover rates and disturbances (e.g. harvesting), the DOM module will then calculate the losses due to decomposition, with the outputs from the pool then applied in both the atmosphere and a soil carbon module. 

In the example above, the order of operation within a timestep is important. The results from the DOM module operations will depend on if they operate on the DOM pools before or after the new carbon is added from the plants. 


## **_How do I create a new module?_**

Firstly it is important to ensure that there is not an existing module that can be used, either in its current state or with minimal modification. Existing modules are available on the moja global github account.

Where an entirely new module is required, there are three initial high-level steps

1. Based on user needs, identify the required model/s
2. Identify all the required input data, including parameters, variables and if the data needs to be spatial, spatially referenced or aspatial. 
3. Identify the pools the module will operate on.

Describe 

1. the development environment for editing modules
2. a template for the minimum viable module
3. testing and debugging


## **_Choosing a language_**

The majority of the FLINT is written in C++. This was a core design decision to allow for the fast processing required when running high spatial/temporal resolution modules. 

However, many scientists are not comfortable with C++ and would prefer to develop and improve their models in languages such as Python and R. In this case it is possible to wrap the models. This will have performance implications, but is often suited for testing.  


### **_Translating a system of equations into a new module_**

Most modules come from a scientific paper with equations that need to be transcribed into something FLINT can read. Usually, they have inputs, parameters and results. 

### **_Wrapping existing models_**

FLINT can be used to make familiar models spatially explicit. The module template can be used to make calls to R or Python models, then feed the results back into FLINT. This allows for researchers to parameterise their model in a familiar environment, while leveraging the spatially-explicit framework of FLINT.

For large runs, calling external libraries will always be slower than transcribing the model to C++. We therefore focus on writing C++ in this document but will revisit this section in the future.

# **Creating and adding a module to FLINT**

Link to the moja.modules.template source code: [https://github.com/moja-global/FLINT.Example/commits/template](https://github.com/moja-global/FLINT.Example/commits/template) 


Registering module in libraryfactory.cpp

1. Import the library factory header file (*libraryfactory.h*) and all the headers of the included transforms and modules.
2. We need to register a module using CMake from *CMakeLists.txt*. Define a MODULE_API which will be referenced in all the header files present in the module. 

    Starting from *FLINT.example\Source\moja.flint.example.base\CMakeLists.txt* as a scaffold copy it to *moja.flint.modulename/CMakeLists.txt*, and make the following changes

*   Rename the `set(PACKAGE "base")` to  `set(PACKAGE "ModuleName")`
*   Under `set(PROJECT_MODULE_HEADERS`, add all the module header files like `landusemodule.h`
e.g.
    
    ```cmake
    set(PROJECT_MODULE_HEADERS
    	#include/moja/flint/example/${PACKAGE}/xxx.h
    	#include/moja/flint/example/${PACKAGE}/errorscreenwriter.h
    include/moja/flint/example/${PACKAGE}/landusemodule.h
    )
    ```

- Under   `set(PROJECT_MODULE_SOURCES` , add all the module source code files like `landusemodule.cpp`
  
  Eg

  ```cmake
  set(PROJECT_MODULE_SOURCES
  #src/xxx.cpp
  #src/errorscreenwriter.cpp
  src/landusemodule.cpp
  )
  ```

  - Add the transform header files under `set(PROJECT_TRANSFORM_HEADERS`

- ```cmake
  set(PROJECT_TRANSFORM_HEADERS
        include/moja/modules/${PACKAGE}/hansenforestcovertransform.h
  )
  ```

- And add the transform source code files under 

  ```cmake
  set(PROJECT_TRANSFORM_SOURCES
  	#src/xxx.cpp
    src/hansenforestcovertransform.cpp
  )
  ```

- In libraryfactory.h, edit the #define and include the _modules.ModuleName_exports.h file with the librarymanager.h file and add #endif at the end.
- Edit the *other/libraryfactory.cpp*. Include the *libraryfactory.h* header and headers of all the included modules (`ExampleModule`),disturbance events and transforms.
- For every module we need to add this line (in this example we are adding the `DisturbanceEventModule` )

- ```c++
  outModuleRegistrations[index++] = ModuleRegistration{ "DisturbanceEventModule", []() -> flint::IModule* { return new DisturbanceEventModule(); }};
  ```

to register a module under `MOJA_LIB_API` 

- ```c++
  int getModuleRegistrations(ModuleRegistration* outModuleRegistrations)
  ```

And similarly for all included transforms,add  

- ```c++
  outTransformRegistrations[index++] = TransformRegistration{ "TimeSeriesTransform",[]() -> flint::ITransform* { return new TimeSeriesTransform(); } }; 
  ```

  under

- ```cpp
   MOJA_LIB_API int getTransformRegistrations(TransformRegistration* outTransformRegistrations) 
  ```

This code will create an instance of the IModule class or ITransform class.
The purpose of the LibraryFactory is to tell the framework what to construct (and how to construct it) when one of those JSON config files asks for something by its key.Eg. To use `LocationIdxFromFlintDataTransform` we write the code

```c++
 outTransformRegistrations[index++] =
   TransformRegistration{"LocationIdxFromFlintDataTransform",
              []() -> flint::ITransform* { return new LocationIdxFromFlintDataTransform(); }};
```

-And then the JSON config file would look like 

- ```json 
  "initial_age": {
      "transform": {
        "library": "internal.flint",
        "type": "LocationIdxFromFlintDataTransform",
        "provider": "RasterTiled",
        "data_id": "initial_age"
      }
    },
  ```

- Finally the file should look somewhat like this:

- ```c++
  // Instance of common data structure
  extern "C" {
	MOJA_LIB_API int getModuleRegistrations(ModuleRegistration* outModuleRegistrations) {
		int index = 0;
		outModuleRegistrations[index++] = ModuleRegistration{ "ExampleModule", []() -> flint::IModule* { return new ExampleModule(); } };
		return index;
	}
	MOJA_LIB_API int getTransformRegistrations(TransformRegistration* outTransformRegistrations) {
		int index = 0;
		outTransformRegistrations[index++] = TransformRegistration{ "ExampleTransform",	[]() -> flint::ITransform* { return new ExampleTransform(); } };
		return index;
	}
	MOJA_LIB_API int getFlintDataRegistrations(FlintDataRegistration* outFlintDataRegistrations) {
		auto index = 0;
		return index;
	}
	MOJA_LIB_API int getFlintDataFactoryRegistrations(FlintDataFactoryRegistration* outFlintDataFactoryRegistrations) {
		auto index = 0;
		return index;
	}
  ```

- ```c++
  MOJA_LIB_API int getDataRepositoryProviderRegistrations(moja::flint::DataRepositoryProviderRegistration* outDataRepositoryProviderRegistration) {
		auto index = 0;
		return index;
	}
  }
  ```

- ```c++
  MOJA_LIB_API int getFlintDataRegistrations(FlintDataRegistration* outFlintDataRegistrations) {
		auto index = 0;
		//outFlintDataRegistrations[index++] = FlintDataRegistration{ "RunStatistics", []() -> flint::IFlintData* { return new RunStatistics(); } };
		return index;
	}
  ```

- ```c++
  MOJA_LIB_API int getFlintDataFactoryRegistrations(FlintDataFactoryRegistration* outFlintDataFactoryRegistrations) {
	auto index = 0;
	return index;
  }
  ```
`FlintDataRepositoryProviderRegistration` is used to import different types of data through a data provider. E.g. the optional *moja.modules.gdal* package implements a provider for reading from rasters using GDAL
The thing they all have in common is that first string arg, which is what links the code to the JSON config files; for example, in *moja.modules.gdal*'s libraryfactory.cpp file, we can see this:

- ```c++
  outDataRepositoryProviderRegistration[index++] = DataRepositoryProviderRegistration{
   "RasterTiledGDAL", static_cast<int>(datarepository::ProviderTypes::Raster),
   [](const DynamicObject& settings) -> std::shared_ptr<datarepository::IProviderInterface> {
     return std::make_shared<datarepository::ProviderSpatialRasterTiled>(
       std::make_shared<RasterReaderFactoryGDAL>(), settings);
   }};
  ```

- And then a simulation could use that in its provider JSON config file (the "type" and "library" parts are the relevant bits - note how the "type" value matches the name in the *DataRepositoryProviderRegistration*):

- ```json
  {
  "Providers": {
    "Spatial": {
      "layers": [
        {
          "name": "initial_age",
          "layer_path": "..\\layers\\tiled\\initial_age_moja.tiff",
          "layer_prefix": "initial_age_moja"
        }
      ],
      "blockLonSize": 0.1,
      "tileLatSize": 1.0,
      "tileLonSize": 1.0,
      "cellLatSize": 0.00025,
      "cellLonSize": 0.00025,
      "blockLatSize": 0.1,
      "type": "RasterTiledGDAL",
      "library": "moja.modules.gdal"
      }
    }
  }
  ```

**Create module.h and module.cpp files**
After registering the modules, we can start writing the module specific code. We will start with importing the necessary header files first which are `#include <moja/flint/modulebase.h>` and `#include _modules.ModuleName_exports.h` followed by module specific files required according to the use case of the module. 
In the case of our *ExampleModule*, the *ExampleModule.h* file should look like this:

- Import `modulebase.h`.

```c++
#include <moja/flint/modulebase.h>
```

- Declare the moja → flint → example → examplemodule namespace

- ```c++
  namespace moja {
  namespace flint {
  namespace example {
  namespace examplemodule{
  ```

- Create an *ExampleModule* class with *EXAMPLE_MODULE_API* which was specified earlier in  `_modules.ExampleModule_exports.h` and extend the class from the *ModuleBase* class in the flint class followed by initialising the newly created class with its default constructor and using virtual to refer to this particular class.
  All the variables declared in this class will be used in the module CPP file. In the example screenshots, variables and members defined in *rothcmodule.h* are used in rothcmodule.cpp.

![img](https://lh3.googleusercontent.com/ZnMbG-6fZJKMZN8JM9rNbroaE5qpCuT3Z2YWCPjaeejKkaEbK5tjCiHH5mPok1j_0CazHT6IUOH_ffmSxhmECagPvIy7WrRpzZOZtIGdWnhEQtSQdoqXHaLApxwtjjg7AmBIbwwm)

![img](https://lh4.googleusercontent.com/1W9wrYkijagseM_lpxlMhdw7hoDiKzLM-3KJciduetb9xMzo2Jn7TAuqAMBDY_moolx3BdaZMGKxNGcz7BKfZwTYakEOwmmshB849GbBnlUi1e4AMOD3invSQnyNvA9Q4t_uFR_v)


The variables defined in the header files (.h extension) are used in the module C++ files (.cpp extension)

- ```c++
  class EXAMPLE_MODULE_API ExampleModule : public flint::ModuleBase {
  public :
   ExampleModule () = default;  
  virtual ~ExampleModule () = default;
  ```

- Next declare the `configure()` function. This method is used to pass extra information from the config files directly to a module. By changing the values in the JSON config files, we can modify the simulation and events in different ways. The FLINT will read the config files and make the changes in the current simulation accordingly. For example:

- ```c++
  void configure(const DynamicObject&) override;
  ```

- The `subscribe()` function will connect the module to the notification handler and details of every event will be passed to the module. The module can then use these details in the simulation. e.g. If a `SprayFertiliser()` event takes place then the module will be alerted and it can use the passed values for calculations of the soil.

- ```c++
  void subscribe(NotificationCenter& notificationCenter) override;
  ```

- `onLocalDomainInit()` & `onPreTimingSequence()`  are functions from *imodule.h* library which will run some code at specific time. For example `onPreTimingSequence()` will run before time starts in the simulation. The code to be run has to be written in the appropriate function. More functions are available which can be used to customise the way we want to run at specific times or events.

- ```c++
  void onLocalDomainInit() override;
  void onPreTimingSequence() override;
  ```

- Under the private access specifier, define all the variables that will be used in the module development (depends on the science).

- ```c++
  private:
	double a;
	double b;
    double temp;
	const flint::IPool* _PoolA;
	const flint::IPool* _PoolB;
	const flint::IPool* _PoolC;
    double ratio_1;
    double ratio_2;
	const flint::IVariable* _presCM;
  ```

- Finally close all the namespaces

```c++
} } } }
```

In the module.cpp file

- Import all the necessary headers and namespaces

- Call the `configure()` function

- Call the `subscribe` function to connect the module to the NotificationCenter.

  ```c++
  void ExampleModule ::subscribe(NotificationCenter& notificationCenter) {
  	notificationCenter.subscribe(signals::TimingInit, &ExampleModule ::onTimingInit, *this);
  	notificationCenter.subscribe(signals::TimingStep, &ExampleModule ::onTimingStep, *this);}
  ```

- In the example, the `ExampleModule` will be notified with the signals

  - `onTimingInit` - On start of the simulation (before time starts)
  - `onTimingStep` - On each timing step (eg. month or days)

- Now let's follow and understand the above steps with reference to the base test module (a part of FLINT.Example) at :

  Header file libraryfactory.h : https://github.com/moja-global/FLINT.Example/blob/master/Source/moja.flint.example.base/include/moja/flint/example/base/libraryfactory.h

  Header file libraryfactory.cpp: https://github.com/moja-global/FLINT.Example/blob/master/Source/moja.flint.example.base/src/libraryfactory.cpp

  Header file testmodule.h : https://github.com/moja-global/FLINT/blob/develop/Source/moja.flint/include/moja/flint/testmodule.h

  Source file testmodule.cpp :https://github.com/moja-global/FLINT/blob/develop/Source/moja.flint/src/testmodule.cpp

## **libraryfactory.h**

Required imports

```c++
#ifndef MOJA_FLINT_EXAMPLE_BASE_LIBRARYFACTORY_H_
#define MOJA_FLINT_EXAMPLE_BASE_LIBRARYFACTORY_H_
#pragma once
#include "moja/flint/example/base/_modules.base_exports.h"
#include <moja/flint/librarymanager.h>
```
Open namespace
```c++
namespace moja {
namespace flint {
namespace example {
namespace base {
```
Create instances of the data structures used The **extern** "**C**" keyword tells a C++ compiler not to mangle (rename) **C** function names.
- ```c++
  extern "C" MOJA_LIB_API int getModuleRegistrations					(moja::flint::ModuleRegistration*	outModuleRegistrations);
  extern "C" MOJA_LIB_API int getTransformRegistrations				(moja::flint::TransformRegistration*	outTransformRegistrations);
  extern "C" MOJA_LIB_API int getFlintDataRegistrations (moja::flint::FlintDataRegistration*	outFlintDataRegistrations);
  extern "C" MOJA_LIB_API int getFlintDataFactoryRegistrations		(moja::flint::FlintDataFactoryRegistration*	outFlintDataFactoryRegistrations);
  extern "C" MOJA_LIB_API int getDataRepositoryProviderRegistrations	(moja::flint::DataRepositoryProviderRegistration*	outDataRepositoryProviderRegistration);
  ```
- Close namespace
- ```c++
  }}}} // moja::flint::example::base
  #endif	// MOJA_FLINT_EXAMPLE_BASE_LIBRARYFACTORY_H_
  ```
## **libraryfactory.cpp**
- Required imports
```cpp
#include "moja/flint/example/base/libraryfactory.h"
#include "moja/flint/example/base/errorscreenwriter.h"
#include "moja/flint/example/base/timeseriestransform.h"
using moja::flint::IModule;
using moja::flint::ITransform;
using moja::flint::IFlintData;
using moja::flint::ModuleRegistration;
using moja::flint::TransformRegistration;
using moja::flint::FlintDataRegistration;
using moja::flint::FlintDataFactoryRegistration;
using moja::flint::DataRepositoryProviderRegistration;
```
- Open namespace
```c++
namespace moja { namespace flint { namespace example { namespace base {
```
- Open extern
```c++
extern "C" {
```
- Register modules
Create a `getModuleRegistrations()` function which registers and initializes the module in a queue `outModuleRegistrations`,and we track the current index of the queue with the index variable. 
```c++
MOJA_LIB_API int getModuleRegistrations(ModuleRegistration* outModuleRegistrations) {
		int index = 0;
		outModuleRegistrations[index++] = ModuleRegistration{ "ErrorScreenWriter", []() -> flint::IModule* { return new ErrorScreenWriter(); } ;
		return index;
	}
```
- We use the `flint::IModule*` class to derive an instance of the `ErrorScreenWriter`, and pass it through `ModuleRegistration` structure (it takes the module name and the module initializer pointer) and then insert it at the end in the `outModuleRegistrations` queue with `outModuleRegistrations[index++]`.
```c++
outModuleRegistrations[index++] = ModuleRegistration{ "ErrorScreenWriter", []() -> flint::IModule* { return new ErrorScreenWriter(); } };
```
- Now similarly register a Transform using the `getTransformRegistrations` which is derived from `flint::ITransform*` class and insert it in the `outTransformRegistrations` queue. In the example we register the `CompositeTransform`. The registration code is commented out.
```c++
MOJA_LIB_API int getTransformRegistrations(TransformRegistration* outTransformRegistrations) {
		int index = 0;
		outTransformRegistrations[index++] = TransformRegistration{ "TimeSeriesTransform",	[]() -> flint::ITransform* { return new TimeSeriesTransform(); } };
		return index;
	}
```
```c++
MOJA_LIB_API int getDataRepositoryProviderRegistrations(moja::flint::DataRepositoryProviderRegistration* outDataRepositoryProviderRegistration) {
		auto index = 0;
		//outDataRepositoryProviderRegistration[index++] = DataRepositoryProviderRegistration{ "RasterTiledBeast", static_cast<int>(datarepository::ProviderTypes::Raster), [](const DynamicObject& settings) ->std::shared_ptr<datarepository::IProviderInterface> { return std::make_shared<datarepository::ProviderSpatialRasterTiled>(std::make_shared<RasterReaderFactoryBeast>(), settings); } };
		return index;
	}
```
## **testmodule.h**
- Required imports
```c++
#ifndef MOJA_FLINT_TESTMODULE1_H_
#define MOJA_FLINT_TESTMODULE1_H_
#include "moja/flint/ioperationresult.h"
#include "moja/flint/ipool.h"
#include "moja/flint/modulebase.h"
```
- Open namespace
```c++
namespace moja {
namespace flint {
```
- Create class FLINT_API from the MoudleBase class:
```c++
class FLINT_API TestModule : public ModuleBase { 
	public:
```
- Under the public access specifier
Initialise the default constructor.
```c++
TestModule() = default;
```
- Initialise the default destructor.
```c++
virtual ~TestModule() = default;
```
- Declare the configure function and the subscribe function.
```c++
void configure(const DynamicObject& config) override;
void subscribe(NotificationCenter& notificationCenter) override;
```
- Declare the onLocalDomainInit, onTimingInit, onTimingStep functions. 
```c++
 void onLocalDomainInit() override;
 void onTimingInit() override;
 void onTimingStep() override;
```
- Under the private access specifier :
```c++
 private:
```
- Declare the pools
```c++
const flint::IPool* _pool1;
const flint::IPool* _pool2;
const flint::IPool* _pool3;
```
- Declare FLINT variables
```c++
const flint::IVariable* _variable1;
const flint::IVariable* _variable2;
const flint::IVariable* _variable3;
```
- Declare settings to be taken from the config files
  These variables refer to the ratio of the carbon transfer
```c++
double ratio_1;
double ratio_2;
double ratio_3;
```
- Declare additional C++ variables for variables and pools
```
 std::string variable_1;
 std::string variable_2;
 std::string variable_3;
 std::string pool_1;
 std::string pool_2;
 std::string pool_3;
```
- Close the class
```c++
};
```
- Close the namespaces
```c++
} // namespace flint
} // namespace moja
#endif // MOJA_FLINT_TESTMODULE1_H_
```
## **testmodule.cpp**
- Required imports
```c++
#include "moja/flint/testmodule.h"
#include "moja/flint/ilandunitdatawrapper.h"
#include "moja/flint/ioperation.h"
#include "moja/flint/ivariable.h"
#include <moja/notificationcenter.h>
#include <moja/signals.h>
```
- Open namespace
```c++
namespace moja {
namespace flint {
```
- Make the configure function to get the data from the config files and pass them to the FLINT simulation variables
```c++
void TestModule::configure(const DynamicObject& config) {
```
- Define variables  
```c++
  ratio_1 = 0.50;
  ratio_2 = 0.50;
  ratio_3 = 0.50;
  variable_1 = "variable 1";
  variable_2 = "variable 2";
  variable_3 = "variable 3";
   pool_1 = "Pool 1";
   pool_2 = "Pool 2";
   pool_3 = "Pool 3";
```
Now we have 2 possibilities :
1. We specify the values of the variables in the config file. 
*   In this case the values need to be passed from the config `DynamicObject` (which holds the whole config JSON file) to the variables in the simulation.
2. We DO NOT specify the values of the variables in the config file.
*   In this case, FLINT will use the default values defined above.
If the config file contains values specified for `ratio_1`, replace the default value of `ratio_1 `with the one in the config file.
```c++
if (config.contains("ratio_1")) {
      ratio_1 = config["ratio_1"];
   }
```
- Similarly, do the same with `ratio_2` and `ratio_3`
```c++
if (config.contains("ratio_2")) {
      ratio_2 = config["ratio_2"];
   }
if (config.contains("ratio_3")) {
      ratio_3 = config["ratio_3"];
   }
```
- Similarly do the same with `variable_1`, `variable_2`, `variable_3 `and `pool_1`, `pool_2`, `pool_3`.`  `
  `extract&lt;const std::string>(); ` converts the JSON to readable C++ string as all the variables are of string data type.
```c++
   if (config.contains("variable_1")) {
      variable_1 = config["variable_1"].extract<const std::string>();
   }
   if (config.contains("variable_2")) {
      variable_2 = config["variable_2"].extract<const std::string>();
   }
   if (config.contains("variable_3")) {
      variable_3 = config["variable_3"].extract<const std::string>();
   }
//-------------------------------------------------------------------------
   if (config.contains("pool_1")) {
      pool_1 = config["pool_1"].extract<const std::string>();
   }
   if (config.contains("pool_2")) {
      pool_2 = config["pool_2"].extract<const std::string>();
   }
   if (config.contains("pool_3")) {
      pool_3 = config["pool_3"].extract<const std::string>();
   }
```
- Make a subscribe function from the TestModule class which takes a `NotificationCenter `pointer called `notificationCenter` as input.
```c++
void TestModule::subscribe(NotificationCenter& notificationCenter) {
```
- Now connect the `notificationCenter` pointer to the `LocalDomainInit ` from the signals class (`signals`), and the reference to our `onLocalDomainInit` function from our `TestModule `class (`&TestModule::onLocalDomainInit`) and this pointer (`*this`) which refers to this `subscribe `function itself.
```c++
notificationCenter.subscribe(signals::LocalDomainInit, &TestModule::onLocalDomainInit, *this);
```
- Similarly define functions for the `TimingInit` & `TimingStep` signals.
```c++
 notificationCenter.subscribe(signals::TimingInit, &TestModule::onTimingInit, *this);
 notificationCenter.subscribe(signals::TimingStep, TestModule::onTimingStep, *this);
}
```
- Now create the `onLocalDomainInit` function
```c++
void TestModule::onLocalDomainInit() {
```
- Get the values of `pool_1` from the `_landUnitData` object with the `getPool` function
  (`_landUnitData->getPool(pool_1);`) and assign it to `_pool1` variable in our simulation.
```c++
   _pool1 = _landUnitData->getPool(pool_1);
```
- Similarly for `pool_2` and  `pool_3`
```c++
   _pool2 = _landUnitData->getPool(pool_2);
   _pool3 = _landUnitData->getPool(pool_3);
```
- Now get the values of `_variable1` from the `_landUnitData` object with the `getVariable` function 
  (`_landUnitData->getVariable(variable_1);`) and assign it to `variable_1` variable in our simulation.
```c++
   _variable1 = _landUnitData->getVariable(variable_1);
```
- Similarly for `_variable2` and  `_variable3`
```c++
_variable2 = _landUnitData->getVariable(variable_2);
_variable3 = _landUnitData->getVariable(variable_3);
```
- Declare the `onTimingInit()` function 
  `void TestModule::onTimingInit() {}` 
- Declare the `onTimingStep()` function. This function will run at every TimeStep.
```c++
void TestModule::onTimingStep() {
```
- From `_landUnitData`, call the `createProportionalOperation()` function (it will return a pointer which points to the `LandUnitWrapper` and hence directly connects to the input data of the current simulation). This function will create an operation called Proportional Operation. 
  More info about Operations in FLINT here: [1.2 Operations · moja-global/FLINT Wiki ](https://github.com/moja-global/FLINT/wiki/1.2-Operations)
- Create a variable, `operation` of the `auto` type (i.e. C++ will set the type of this variable for you) and set it to the `_landUnitData->createProportionalOperation()`. Hence we have a Proportional Operation and stored it in the `operation` variable.
```c++
 auto operation = _landUnitData->createProportionalOperation();
```
- Now we use the operation to add a Transfer of carbon Pool 1 to Pool 2 in some ratio (e.g. if ratio=0.5, 50% of total carbon in Pool 1 is transferred to Pool 2.) using the `addTransfer` function.
- This line of code will run `addTransfer()` with `_pool1` and `_pool2` as the source and destination pools respectively and transfer (`ratio_2` * total carbon of `pool_1`) from `_pool1` to `_pool2`.
```c++
operation->addTransfer(_pool1, _pool2, ratio_1)
```
- Similarly this line of code will transfer  (`ratio_2` * total carbon of `pool_2`) from `_pool2` to `_pool3`.
```c++
->addTransfer(_pool2, _pool3, ratio_2)
```
- And thus this final line of code will transfer  (`ratio_3` * total carbon of `pool_3`) from `_pool3` to `_pool1`. The semicolon at the end will complete the operation sequence. The operations are run in the order they are defined in code.
```c++
->addTransfer(_pool3, _pool1, ratio_3);
```
- Finally use the submitOperation of the `_landUnitData` to submit our operation variable of the type (ProportionalOperation) to the FLINT LandUnitController to run the operations on the input data.
- Finally close the onTimingStep() function
```c++
}
```
- And in the end close the necessary namespaces
```c++
}  // namespace flint
}  // namespace moja
```
## **Configuration files in FLINT** 
While running moja.cli, we pass the config file as a parameter to the FLINT. The config variable used in the module source code is of the DynamicObject type (a type from POCO library). This config variable holds the whole JSON config file and then is used to modify the simulation.
e.g. This is a config JSON file
```json
{
      "eventqueue": {
        "flintdata": {
          "library": "internal.flint",
          "type": "EventQueue",
          "settings": {
            "events": [
              {
                "date": {
                  "$date": "2000/01/01"
                },
                "id": 1,
                "type": "agri.NFertEvent",
                "name": "Synthetic fertilizer",
                "quantity": 100,
                "runtime": 5
              },
              {
                "date": {
                  "$date": "2001/05/01"
                },
                "id": 2,
                "type": "agri.NFertEvent",
                "name": "Organic fertilizer",
                "quantity": 200,
                "runtime": 5
              },
```
Some parts of the config files (especially involving `flintdata` as it is a generic data type to hold objects) may be exclusive to a particular module implementation.
Explanation of terms
*   `library` : The library to be used (eg. `moja.modules.cbm` (GCBM), `moja.flint.example.agri` (agricultural soil module), `internal.flint` (FLINT Core library))
*   `"type": "EventQueue"` : Type of the variable inside `flintdata`/ `EventQueue `. It will be an event if it is inside an `EventQueue` like ( `agri.NFertEvent `Fertilising event),else it can be any variable type as `flintdata` supports all kinds of C++ objects like  `  SpatialLocationInfo`  or transforms like `CompositeTimeSeriesTransform.`
*   `"id" ` / `"order"` : The position of the event in the Event Queue
*   `"Name"`: Name of the event/operation to be used. Eg. Plant Dryland Forests (chapman_richards.ForestPlantEvent), Simple(operationManager), Manure Management Event (agri.ManureManagementEvent),etc.
*   `"quantity"`: Quantity of stock units (could be carbon, nitrogen,etc.)
*   `"settings"`: Usually refers to flags related to FLINT runs. Eg.`         	"debugging_enabled"` can be used to enable/disable debugging.` `
*    `"Variables"`: This object holds all kinds of FLINT variables like `"ipcc_climate_zone"` or `"region"`
And this is the method from the module CPP file.
```c++
void NFertEvent::configure(DynamicObject config, const flint::ILandUnitController& landUnitController, datarepository::DataRepository& dataRepository) {
   DisturbanceEventBase::configure(config, landUnitController, dataRepository);
   quantity = config["quantity"];
   runtime = config["runtime"];
}
```
In this way we are able to set the quantity and the runtime of the Synthetic fertiliser to 100 and 5 and the organic fertiliser to 200 and 5 respectively.
Another example; The JSON config file:
```json
 "CBMGrowthModule": {
        	"enabled": true,
        	"order": 7,
        	"library": "moja.modules.cbm",
        	"settings": {
            	"debugging_enabled": true
       	 }    	},
```
Where everything in the optional “settings” section is what gets passed as the argument to `void configure(const DynamicObject&) `(which was declared in the module header file). 
Then the growth module code looks like:`   `
```c++
 void YieldTableGrowthModule::configure(const DynamicObject& config) {
        if (config.contains("smoother_enabled")) {
        	_smootherEnabled = config["smoother_enabled"];
        	_volumeToBioGrowth->setSmoothing(_smootherEnabled);
    	} 
        if (config.contains("debugging_enabled")) {
        	_debuggingEnabled = config["debugging_enabled"];
    	} 
        if (config.contains("debugging_output_path")) {
        	_debuggingOutputPath = config["debugging_output_path"].convert<std::string>();
    	}	}
```
In the example we are enabling the “debugging_enabled” flag by setting it to “true”. Hence we can enable settings such as `smoother_enabled `by setting `"smoother_enabled": true `in the config files.
### **BuildLandUnit Module**
A key module for all FLINT implementations is the Build Land Unit Module (BLUM). The BLUM defines the rules of developing the sequence of events and processes using both spatial data as well as database data. The rules within BLUM are configured based on the input data logic, and are restricted through the code to the key design decisions of FLINT custom implementation (e.g. SLEEK or GCBM). In simple terms, the BLUM can be considered as a series of ‘if, then’ statements. For example, _if _land cover changes from X to Z, _then _run Y events, or _if _tree status equals true, _then_ apply the Tree Growth module. As with most modules, the BLUM consists of multiple modules, each completing a specific computational process.  
### **ErrorScreenWriter Module**
Another key module for all FLINT implementations. The ESW Module is used to log every timestep and show the output on the screen. This is extremely useful to check what is happening at every time step during module simulation. 
### **SystemSettings**
It is a data structure that contains variables or objects(collections) to aggregate information which can be sent to and shared amongst modules. It is a data structure that has a few system bits of information in it (see Chapman Richards example). In this way, one class of data that is loaded once in the SystemSettings can be passed to all the objects and each module can use it as it wants. Hence every time we change module settings, we don't have to go and edit in lots of places. One class or one data structure that has all our information in it.
In Chapman Richards we have the system settings in *moja.modules.chapman_richards/include/moja/modules/chapman_richards/systemsettings.h*. We can see that the SystemSettings are used in many modules/files like *landunitsqlitewriter.h*, *aggregatorlandunit.h*, *buildlandunitmodule.h*.
The `ObjectHolder::Instance()` is providing a Singleton of that class, which has `systemSettings` and various collections. Those collections are used to store & aggregate important data across multiple LUs simulated. For example: `date_dimension` stores all the unique dates simulated, and IDs from these records could be used in other records to link them to dates. Like a DB star schema.
Some Modules take the `ObjectHolder& commonData`, some take only the `SystemSettings`. Depending on the data they need I guess. Collections vs only some settings.
The module registration in *libraryfactory.cpp* for each module is given a handle to the data required.
In `outModuleRegistrations` , `ObjectHolder::Instance().systemSettings` or `ObjectHolder::Instance()`
Shows that the modules can be more complex, and interconnectivity can be built with other means (C++) rather than the fixed FLINT Variables interfaces available.
To use SystemSettings with a module, we need to import the systemsettings.h in the module header file (e.g. landunitsqlitewriter.h) and add `ObjectHolder::Instance().systemSettings `  or `ObjectHolder::Instance());` in  `getModuleRegistrations`  in libraryfactory.cpp.
```c++
MOJA_LIB_API int getModuleRegistrations(flint::ModuleRegistration* outModuleRegistrations) {
   auto index = 0;
   outModuleRegistrations[index++] = flint::ModuleRegistration{
       "AggregatorError",
       []() -> flint::IModule* { return new AggregatorError(ObjectHolder::Instance().systemSettings); }};
```
## **Disturbance Events**
## **_What are Disturbance Events?_**
Disturbance events refer to natural and human induced events like deforestation, forest fires etc. These events can have three impacts on an ecosystem that the FLINT needs to be able to address. 
1. Move stocks from one pool to one or more other pools
2. Change the subsequent fluxes of pools over time
3. Change the ecosystem type.
### **Moving stocks from one pool to another**
The simplest way to think of a disturbance event is as a table where carbon is moved from one pool to another. In the example below, carbon is moved from the tree biomass to products and debris following a harvest event. 
<table>
  <tr>
   <td>
   </td>
   <td><strong>Tree AGB</strong>
   </td>
   <td><strong>Tree BGB</strong>
   </td>
   <td><strong>Dead material</strong>
   </td>
   <td><strong>Litter</strong>
   </td>
   <td><strong>Soil</strong>
   </td>
   <td><strong>Products</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Tree AGB</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>0.6</strong>
   </td>
   <td><strong>0.1</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>0.3</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Tree BGB</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>1</strong>
   </td>
   <td><strong>0</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Dead material</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>.1</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Litter</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Soil</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
  </tr>
  <tr>
   <td><strong>Products</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
   <td><strong>-</strong>
   </td>
  </tr>
</table>
### **Changing fluxes**
Often an event will impact the functioning of an ecosystem. For example, it may change plant growth (such as through thinning responses or fertiliser) or decomposition rates. Modules will need to know when such events have occurred.
### **Change the ecosystem type**
In some cases an event will change the ecosystem type. For example, following deforestation the ecosystem will change from forest to agriculture. This will lead to new modules being called and older modules being ignored.  
### **Disturbances in FLINT**
*   During a FLINT run, the Sequencer handles the time with timesteps with an event queue. Disturbance events break the normal sequence of events in the event queue.   \
Examples: 
    *   If an event occurs every week, then the particular time step (month)  is divided into 4 parts. 
    *   Assume that a set of events occur exactly the same, and are two seconds apart. Then the first event will break the time step, then two seconds will pass and after that the second event will occur, two seconds will pass, the third event will occur, and so on. Hence the time step gets broken up into as many parts as it's required to give all the events the time to occur and they do their own carbon pool moves.
*   The disturbance event module will register to catch the system event `signals::DisturbanceEvent` it registered the method `DisturbanceEventModule::disturbanceEventHandler()`.That can simply call the events simulate() method. In the example you referenced it is a little convoluted. The Derived class `simulate()` is called, which in turn calls the event_handler.simulate().  \
see:
    `void ForestPlantEvent::simulate(DisturbanceEventHandler& event_handler) const { event_handler.simulate(*this); }  \
`The event code could've handled the modelling, but in this case all the modelling code has been maintained in the event handler. Some flags are set and some transfers (operations) and made.
```c++
operation->addTransfer(agcm_, atmosphere_, 1.0)->addTransfer(bgcm_, atmosphere_, 1.0);
forest_exists_->set_value(false); // Clearing Thin
forest_age_->set_value(0.0);
```
## ***System Providers in FLINT***  
### **SQLite Provider**
Suppose we have this provider:
```c++
{
   "Providers": {
       "Database": {
           "path": "..\\input_database\\gcbm_input.db",
           "type": "SQLite"
       },
[...]
```
The simplest type of query we can do would look something like this, in our "Variables" config:
```c++
       "disturbance_type_codes": {
           "transform": {
               "queryString": "SELECT dt.name AS disturbance_type, dt.code AS disturbance_type_code FROM disturbance_type dt",
               "type": "SQLQueryTransform",
               "library": "internal.flint",
               "provider": "Database"
           }
       },
```
Where` "provider": "Database"` matches the SQLite provider name: `"Database": {`
We can access that from a module the same way you would any other variable:
```c++
auto distTypeCodes = _landUnitData->getVariable("disturbance_type_codes");
const auto& distTypeCodesValue = distTypeCodes->value();
if (distTypeCodesValue.isVector()) {
	for (const auto& code : distTypeCodesValue.extract<const std::vector<DynamicObject>>()) {
		std::string distType = code["disturbance_type"];
		int distTypeCode = code["disturbance_type_code"];
	}
} else {
	std::string distType = distTypeCodesValue["disturbance_type"];
	int distTypeCode = distTypeCodesValue["disturbance_type_code"];
}
```
Where the first line gets a pointer to the IVariable, the second line (->value()) actually causes the query to execute, and the rest of the lines handle the possible results from the query - multiple rows come back as a std::vector&lt;DynamicObject>, or if there's only a single result, it comes back as a single DynamicObject (not a vector).
SQL can be inline in the JSON config using "queryString": "select * from whatever" or stored in a separate file using "queryFile": "some/query.sql"
We can do some basic format-string type stuff in the SQL:
"`SELECT * FROM species_parameters WHERE species_name LIKE {var:species}`" &lt;-- replaces {var:species} with the value of the variable named species
"`SELECT growth_modifier FROM density_parameters WHERE hardwood_merchantable_carbon >= {pool:HWMerchC}`" &lt;-- replaces `{pool:HWMerchC}` with the value of the HWMerchC pool
"`SELECT is_desert FROM climate_types WHERE precipitation &lt;= {var:climate.mean_annual_precipitation}`" &lt;-- replaces `{var:climate.mean_annual_precipitation}` with the value of the mean_annual_precipitation property of the variable named climate.
# **Key Components of the FLINT**
![img](https://lh5.googleusercontent.com/nXC79wo6F_8i1ELqJDhinPHdDxLnEv3lnx02V4TUrerGcYfHpIFaABgptJKESTlfMOejL-_xCsy1UU4SL3_2wKaqnacNGBW_VlidsJyzZIXtDa_GCsA2gzAUHL9x7ZMOr5BVKfK5)
​													Core Framework Layout
![https://documents.lucidchart.com/documents/821ee480-89eb-4e74-ae28-06dfc476102f/pages/LfbP0ueNlu39?a=1710&x=103&y=202&w=1045&h=836&store=1&accept=image%2F*&auth=LCA%20527692bcb931b22ba9785c39601707e57065aec8-ts%3D1455506483](https://lh6.googleusercontent.com/TlwogBWYILY9qjh_p70llWEQsTkCDYhhjMqBMPL0QTxc-wKlGSguG_0Jx85b7kTg4zlseP18ykImA2ymOeIOVYjBwnLlqTx10l1CMKYsg0NYZHt29jVuU9HNNJYrqheo9zLcQxdG)
​								        	 Examples how the FLINT manages modules and data
![alt_text](images/image2.png "image_tooltip")
​													Simple Diagram of Components
Key components of the FLINT system are:
*   ### **Land Units**
    
    *   A Simulation Unit is a unit for which a module is applied. A Simulation Unit can represent a spatial area, such as a pixel or forest stand, or it can represent an emissions source, such as livestock. Where the Simulation Unit refers to a geographically referenced area, it is known as a Land Unit.
    *   Within the databases and data-layers underpinning the FLINT, there are attributed values that describe the characteristics of each Simulation Unit. For example, for a Land Unit there may be information on the unit’s area, land type, age of vegetation, species and carbon pools. The overall framework of FLINT manages the processing of Simulation Units over time. While Simulation Units are the basis of all simulations run in FLINT, they are rarely used for reporting purposes (see Local Domain).
*   ### **Local Domain**
    
    *   Sitting above Simulation Units, is the Local Domain. The Local Domain is the collective of Simulation Units that are bound for a specific purpose. This can be, for example, to report on the changes in carbon stocks for a particular region.
    *   Within FLINT, Local Domains have three main functions. Firstly, they are used to ‘house’ the variable values for all the simulation units which they represent. Secondly, through a local domain controller they assign these values to the simulation units during a simulation. Thirdly, they receive the output simulation units, and up-date the domain characteristics.
*   ### **Local Domain Controller (LDC - `LocalDomainControllerBase`)**
    
    *   Handles the current iteration of the Land Unit’s intended for simulation.
    *   Including calling the configured Sequencer
    *   **Types of the LDC:**
        *   System Spatial LDC: `SpatialTiledLocalDomainController`
        *   System Point LDC: `LocalDomainControllerBase`
    *   The moja CLI program will use the config reader to instantiate the correct LDC according to type of simulation (point or spatial) as specified in the config files.
    *   Built into the moja CLI program
    *   Users can run default `moja.cli.mulliongroup` (.exe on windows) or create their own. By default it will use the inbuilt LDC in point/spatial mode and is fixed in how it decides to run the Land Units across the spatial area of interest. Users can write their own LDC implementation to extend or modify the existing CLI.
    *   For example, we have added the args -`tile`, -`block`, -`cell`. These args let us specify which tile/block/cell to run in a spatial simulation. The T/B/C indexes represent a lat/Lon in a simple tiles referencing system.
*   **Land Unit Controller (`&landUnitController`)**
    
    *   Each thread gets its own LandUnitController which provides access to pools/variables and carbon transfers for the current pixel.
*   **Land Unit Data (`_landUnitData`)**
    
    *   This is the module’s view of the LandUnitController - it interacts with the land unit controller on behalf of the module to create and submit carbon transfers (createStockOperation/createProportionalOperation/submitOperation) and get references to pools and variables.
  *   **getPool()**
    
        Gets a reference to a pool - usually a carbon pool, but a pool is really just a container for a number representing a quantity of something. Most of the work of a module is just transferring amounts between pools. When a module gets a reference to a pool, it is guaranteed by the framework to always point to the current pixel.
        
        For example
```c++
	_soil = _landUnitData->getPool("soil");
	_atmosphere = _landUnitData->getPool("atmosphere");						
```
- ​			**getVariable()**
Gets a reference to a variable - variables come in two main categories: transforms, which retrieve read-only data, and read/write variables that contain more “state”-type data. Note that read/write variable values persist across pixels: for example, if a variable “a” starts at 0, and a module sets it to 1 after processing a pixel, the variable’s value will still be 1 after moving on to the next pixel. Pool values are different in that they get reset to their starting/default value after every pixel.
For example
```c++
forest_age_ = _landUnitData->getVariable("forest_age");
forest_type_ = _landUnitData->getVariable("forest_type");
```
*   **Notification Center (`NotificationCenter`)**
    *   It is used to control FLINT System events. It is responsible for sending signals to all the current processes in the simulation.
    *   It is a data member of the Local Domain Controller 
    *   Events are fired using the method “`postNotification`” and a System Event Type (see signals.h). Modules can be configured to listen to these events (signals) and ‘react’ by running a particular function at that particular time in the simulation.
*   **Sequencer (`SequencerModuleBase`)**
    *   Called by the LDC, handles iterations (other than location, i.e. date) for each Land Unit.
    *   Normally handles time with the timesteps (i.e. Yearly, Monthly, Daily, etc)
    *   Each Sequence will fire predefined FLINT System Events (InitTiming, TimingStep, etc). These events can be subscribed to by the Modules.
    *   For Disturbance event handling see: `CalendarAndEventFlintDataSequencer`
        *   Allows events to be defined and processed mid step.
        *   Any step with an Event will be broken into 2 or more segments, with Pool moves being proportioned appropriately.
        *   See [chapman richards](https://github.com/moja-global/FLINT.Example/tree/master/Source/moja.modules.chapman_richards) example of event handling
        *   In [Build Land Unit module](https://github.com/moja-global/FLINT.Example/blob/master/Source/moja.modules.chapman_richards/src/buildlandunitmodule.cpp), populating the event queue
*   **Providers (`IProviderInterface`)**
    *   Defined datasets that allow the FLINT to access input data sources such as Relational Databases, Spatial Data, etc.
    *   Defined interface allows location lookup of data when running spatially - Lat & Lon.
    *   Various base types of *Providers* exist to define interfaces available:
        *   `IProviderNoSQLInterface`
        *   `IProviderRelationalInterface`
        *   `IProviderSpatialRasterStackInterface`
        *   `IProviderSpatialRasterInterface` 
        *   `IProviderSpatialVectorInterface`
*   **Datarepository (`moja.datarepository`)**
    *   It contains various types of data providers that read from spatial layers, databases, etc. and make that data available through the various transform classes, and finally through named variables that modules read from. One example would be an `ExternalVariable` with a `LocationIdxFromFlintDataTransform` using a `TileRasterReaderGDAL` provider to read a data value for the current pixel from a spatial layer. 
```c++
void ForestPlantEvent::configure(DynamicObject config, const flint::ILandUnitController& landUnitController,  datarepository::DataRepository& dataRepository) 
{
   DisturbanceEventBase::configure(config, landUnitController, dataRepository);
   forest_type_id = config["forest_type_id"];
} 
```
*   **Operation Manager (IOperationManager)**
    *   This component manages operations created on Pools. Designed to enable Mass Balance and allows fluxes to be timed according to Steps and User preference (i.e. immediately or the end of each timing step). See System Modules:
        *   `TransactionManagerEndOfStepModule` 
        *   `TransactionManagerAfterSubmitModule`
    *   These modules make calls on the Operations Manager to apply registered Operations, but at different times. FullCAM for example will process all operations at the end of each step, GCBM will do them immediately after being submitted.
    *   The timing change has the following effects:
        *   Different sets of science modules have different needs in terms of when the carbon transfers get applied because it affects the view that the modules have of the carbon pools. GCBM is a long chain of mostly sequential modules, so we use `TransactionManagerAfterSubmitModule` which applies the pool operations after every single event the system fires - we want to ensure that all of our modules are looking at the most current pool values, because some of our modules use them to inform the next movements of carbon. Other sets of modules might be more simultaneous - that is, they set up a bunch of proportional operations based on the pool values at the beginning of the timestep, then they all get applied at once. It's also possible to manage this entirely in the science modules - these are really just helpers.
            *   TransationManagerAfterSubmitModule is in there mainly for GCBM, pretty much everything else should be using TransactionManagerEndOfStepModule.
    *   Different implementations can be created. Using different methods to make the movements between Pools. For example, using Matrix tools (i.e. EIGEN) or a simple list method (`OperationManagerSimple).`
    *   addTransfer
        *   After creating an operation (proportional or absolute), which tells the FLINT that the associated transfers are either proportions or specific amounts, we need to add one or more transfers to the operation before submitting it. A transfer is a movement of units (carbon or whatever the pool represents) from one pool to another. For example: `_landUnitData->createProportionalOperation()`, then `addTransfer(softwood, deadwood, 0.5)` would transfer half of the amount in the softwood pool to the deadwood pool.
            Transfers within the same operation occur simultaneously - that is, if the same operation had two transfers instead: 
            *   For example: softwood -> deadwood @ 0.5, softwood -> soil @ 0.5, half of the softwood would go to deadwood and half would go to soil and none would remain.
    *   Submit Operation function
        *   Normally used to execute operations after defining them. Submits an operation (set of pool transfers) to the system. All transfers are tracked by the system so that an output module can read them and write the transfer information to a database or other repository.
            For example:
```c++
submitOperation(operation);
```
Operation types are: 
- Stock  `createStockOperation()`
  - move explicit amounts between specified Pools
   For example (Here `EF_1_value ` stands for Emission Factor)
```c++
auto operation = _landUnitData->createStockOperation();
   operation->addTransfer(soil_, atmosphere_, (fert.quantity * EF_1_value) / fert.runtime);
   _landUnitData->submitOperation(operation);
```
- Proportional `createProportionalOperation()`
  - move a proportion between specified Pools 
For example, here carbon moves from `_pool1` to `_pool2`  in ratio of `ratio_1`, then carbon from `_pool2` moves to `_pool3` in `ratio_2` and so on 
```c++
void TestModule::onTimingStep() {
  auto operation = _landUnitData->createProportionalOperation();
  operation->addTransfer(_pool1, _pool2, ratio_1)
    ->addTransfer(_pool2, _pool3, ratio_2)
    ->addTransfer(_pool3, _pool1, ratio_3);
  _landUnitData->submitOperation(operation);
}
```
*   **Modules (`ModuleBase`)**
    *   Both internal FLINT and custom modules
    *   Attached to a 0 or Many FLINT Events that are fired by the LDC or Sequencer
    *   Can interact with Pools and Variables and use Provider Data
*   **Common Data: Pools (`IPool`)**
    *   Single set of Pool values shared across all Land Units
    *   Can be reset at various stages
    *   Can be Viewed and modified at various time - used to do Pool reporting
    *   Defined interface in interactions - allows mass balance to be maintained
    *   Pools can report more than Carbon - simply a bucket with a Floating Point number, that modules can make moves to and from
*   **Common Data: Variables (`IVariable`)**
    *   Used by the Modules to store data they may need to share with the implementation (other Modules).
    *   For example: Boolean: `TreeExists` would be a boolean to tell other Modules that a tree has been planted.
    *   Variables can also be `moja::flint::ITransform `or `moja::flint::IFlintData`
    *   Transforms allow the system to replace a variable value with a value returned by the Transform - hence calling code that can make calculations and access current Pool, Variable and Provider values.
    *   For example: accessing a Spatial Layer from a `Provider` would automatically get the data for the current Lat & Lon
    *   For example, these variables are declared in header file of the module
```
   flint::IVariable* forest_exists_;
   flint::IVariable* forest_age_;
   flint::IVariable* forest_type_;
```
  Then in the module CPP file they are used in this way
```
  forest_exists_ = _landUnitData->getVariable("forest_exists");
  forest_age_ = _landUnitData->getVariable("forest_age");
  forest_type_ = _landUnitData->getVariable("forest_type");
```
Hence data from `_landUnitData` can be stored in a variable of class `IVariable`.
*   **Common Data: Variables (`IFlintData`)**
    *   `FlintData` allows for a more complex data structure that can be shared across Modules. Giving more that just the single value() returned by a Transform.
        With Variables and Transforms we use the method ->value() ?   allowing use to substitute either of them. `IFlintData` doesn't have this method, so allows more complex object use - if you know what type you're looking for.
        Users can derive a C++ object from the `FlintData` and customise it for various purposes (for eg. importing and storing GIS data in memory). 
        `IFlintData` also allows unpacking from JSON - so can be completely defined in JSON config. An example of this is the `EventQueue` stuff with `CalendarAndEventFlintDataSequencer`
- FLINT.example: FLINT.Example/Run_Env/config/point_forest_config.json has
- ```json
  {
  "eventqueue": {
  "flintdata": {
  "library": "internal.flint",
  "type": "EventQueue",
  "settings": {
  "events": [
  {
  "date": {
  "$date": "2001/01/01"
  },
  "id": 1,
  "type": "chapman_richards.ForestPlantEvent",
  "name": "Plant Dryland Forests",
  "forest_type_id": 1,
  "age": 0.0
  },
  {
  "date": {
  "$date": "2050/01/01"
  },
  "id": 1,
  "type": "chapman_richards.ForestClearEvent",
  "name": "Clear Dryland Forests"
  }
  ]}}}},
  ```
  
![img](https://lh6.googleusercontent.com/FRcDUrPL24eqbpemvJPChMLVMf1dLpMBopPuboV5eIjz2jdi9rBoZ5mUX0stE6KIEogyUe-zkF9YdH5zOnmUEJ0f0BnO-byG8i-iS27CSE3fQhd9M1enZVPlcNzpDlEu3ScexBhP)Not completely accurate, but an indication of how the system is trying to process