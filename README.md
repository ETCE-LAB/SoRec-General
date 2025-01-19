<img src="https://github.com/user-attachments/assets/c86c506a-bfd7-49ce-a798-09f3f591e200" width="300">

# SoRec – Digitalisation of Sorting Processes for Fine-Grained, Metal-Containing Waste Streams in the Recycling Industry

## Overview

Closing material and resource cycles is an integral part of the circular economy. Many products (especially electronic devices) are becoming increasingly complex regarding their structure and raw materials. To recover raw materials, the products must be broken down into ever-finier grain sizes and subsequently sorted. In general, dry sorting processes achieve good sorting results in the fine particle size range with lower throughputs. However, when increasing the throughput, the quality of the sorting results decreases considerably. In order to operate profitably, sorting machines must work as close as possible to the tipping point between good quality and maximum throughput while also minimising maintenance downtimes.

The SoRec project focuses on digitalising sorting processes for fine-grained, metal-containing waste streams in the recycling industry. By installing state-of-the-art industrial line cameras and sensors, we are digitizing the conventional sorting method on a moving belt. With the help of advanced AI models and algorithms in deep learning and machine learning, our system can accurately detect materials on the conveyor belt, classify them based on size, shape, and color, and even find their precise edges. With the capability to identify multiple layers of materials, the AI model provides valuable density and volume estimation. To ensure real-time efficiency and control, we have integrated the AI model with powerful computer vision techniques, which handle crucial image processing tasks. This seamless collaboration between AI and computer vision allows us to estimate the belt’s speed and detect any anomalies, ensuring precise sorting and preventing belt misalignments. Our materials, measuring just 1 mm in size, demand meticulous attention to detail, necessitating high-level zoom capabilities for precise annotations. With this innovative AI-driven system, we are taking a significant step towards automating and optimizing the sorting process, enhancing productivity, and elevating the industry to new heights of accuracy and efficiency.

## System Architecture

### High-Level Architecture
The high-level architecture of the project is shown below. Each box is an independent service or artifact.
The web portal is the interface to the user. Here the user sees bundled information on individual machines and can control them from there. Control works by sending an http request from the web portal to the ManageBackend. This forwards the request to the MachineBackend, which communicates with the machine via a PLC connection. The interpretation works the other way round. The MachineBackend reads the current status of a machine and sends this data to the Manage Backend. This forwards the data to the Azure Digital Graph. An endpoint is then called from the Manage backend via Azure Eventgrid and notified that there is new data in the digital graph. This is then retrieved from the Manage backend and forwarded to the web portal via a socket connection. We use an external identity server for authentication and authorization. In our system, this is a Duende service that adheres to the OIDC standard.

![High-Level]](System-Architecture/20250119_Sorec-High-Level-Architecture.png?raw=true "High-Level Architecture")


### Refined Architecture & Digital Twin

Below is the refined technical architecture of our individual services. 
* Web portal (React): The idea is that UI components manage their data independently. This ensures that they call use cases directly. The use cases then configure an http request that is sent to the management backend. If there are dependencies between Ui components, they must be able to inform each other when things have changed. There are state slices for this. These are used exclusively for change events and do not manage any data.
* ManageBackend (.net): The manageBackend follows a simple architecture. Requests are received in the controller (Rest) or hub (Socket) layer. Communication with the database then takes place either directly via the context (in which the models are registered), a service is called to update the Azure DigitalTwinGraph, for example, or a request is forwarded to the machine backend.
* MachineBackend (Flask): The MachineBackend follows a similarly simple architecture. Here, schedulers are added that measure the status of the machine at defined intervals, evaluate the data using AI models and send relevant data to the ManageBackend via a service.
* DigitalTwin: The digital twins are managed centrally in the Microsoft Azure portal. An Azure Digital Twins instance has been set up that enables a graph-based display of the digital twins and their relationships. The management backend was expanded to include an interface to the Azure Digital Twins API, with which twins can be created, deleted, updated and their relationships defined via the application. These operations can be carried out via the web portal.
The status data of the machines transmitted from the machine backend is processed in the management backend and provided graphically on the machine view page. This integration simplifies precise and needs-based updating of the digital twins in the Azure Digital Twins Graph.

![Refined-Architecture]](System-Architecture/20250119_Sorec-Refined-Architecture.png?raw=true "Refined-Architecture")


### Domain Model

The domain model is the technical core of the system. Classic user management has been implemented. User profiles can be members of an organization. If they are members, they are able to invite other user profiles. They can accept or decline the invitation. Each member of an organization can create locations, i.e. locations where machines are located. There can be any number of machines at a location. Here you can also see which data is viewed for a machine. SpeedOfBelt and SpeedOfDrum can be measured directly. SortingQuality and ThroughPut are calculated by AI models. Cameras that record images are attached to a machine.

![Domain-Model]](System-Architecture/20250119_Sorec-Domain-Model.png?raw=true "Domain Model")



## Contributors (Past and Present)
1. Shohreh Kia
2. Johannes Mayer
3. Mariella Rönn
4. Hooman Taghizadeh
5. Mattes Knigge
6. Benjamin Leiding
