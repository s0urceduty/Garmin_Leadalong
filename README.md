![GPS](https://github.com/user-attachments/assets/bf593fb7-4520-494a-968c-bb63d8840c9f)

Leadalong is Monkey C module designed as a structured multiuser coordination framework built specifically for Garmin devices that support the Connect IQ platform. Its purpose is to transform traditionally single-user GPS navigation into a synchronized group-based experience where multiple participants can operate under a shared state model. Rather than simply broadcasting positions, Leadalong introduces organized constructs such as groups, shared waypoints, shared routes, race overlays, role assignments, and spread monitoring. The architecture is modular, beginning with the Groups submodule as the coordination core, but structured so additional submodules can later extend functionality into areas like synchronized navigation cues, shared objective tracking, cooperative task completion, or real-time expedition management. It is intentionally designed around Connect IQ’s sandbox limitations, meaning it prioritizes lightweight state synchronization, efficient data structures, and periodic update logic rather than heavy real-time streaming.

From a functional perspective, Leadalong enables both cooperative and competitive outdoor dynamics. Cooperative use cases include guided hikes where a leader distributes route updates and monitors group cohesion, cycling teams maintaining pace alignment, or expedition teams tracking spatial separation thresholds for safety. Competitive use cases include dual and triple route races where participants travel in different modes—walking, running, cycling, vehicle, or even aircraft—and compare progression along a shared route. The module tracks participation state, progress percentages, timestamps, and positional updates while remaining device-aware and memory-conscious. By combining structured group state management with extensible routing and race logic, Leadalong serves as a programmable coordination layer that enhances Garmin’s navigation ecosystem without modifying core firmware, making it both scalable and compliant with Connect IQ constraints.

Submodules:
------------------

<details>
<summary>Groups</summary>

Leadalong.Groups is a submodule containing over 30 advanced multiuser navigation and racing functions. The Leadalong.Groups submodule is designed as a structured multiuser coordination system for Garmin Connect IQ devices. Its purpose is to manage organized groups of users participating in shared navigation, activity tracking, and synchronized outdoor movement. The module maintains internal state for group identity, membership, leader designation, shared navigation objects, activity assignments, positional tracking, and group-level settings. It is built around a centralized manager that maintains lightweight data collections and role relationships while operating within Connect IQ’s memory, execution time, and API constraints. The architecture emphasizes modular growth, clean separation of state, and compatibility across supported Garmin devices.

This module is intended to function as a foundation layer that future Leadalong submodules can extend. It supports distributed waypoint structures, route objects, race state management, synchronized data snapshots, and role-based control logic. By maintaining structured collections for members, routes, shared travel data, and group settings, the system creates a consistent internal framework that can scale without increasing architectural complexity. The design prioritizes efficient state handling, predictable execution, and compatibility with Garmin’s sandboxed runtime model while remaining flexible for future expansion.

</details>

<details>
<summary>Search</summary>

The Leadalong.Search submodule functions as a structured search planning and execution engine designed for Garmin Connect IQ devices. It provides a systematic framework for defining search geometries, organizing search sessions into segments, and measuring operational performance over time. Search areas are represented as named geometric constructs composed of coordinate arrays, enabling repeatable patterns such as lines, squares, spirals, and fully custom-defined routes. Each area maintains metadata including creation time, origin parameters, and structural properties, allowing consistent reuse and comparison across sessions. The module maintains internal state collections for active searches, completed segments, visited points, coverage counts, and timing metrics, forming a cohesive data model that supports both guided navigation and performance analytics within the device’s runtime constraints.

Beyond geometry creation, the submodule incorporates measurement and reporting mechanisms that track duration, per-segment timing, coverage percentage, estimated distance, and speed-based time projections. Visitation logic uses configurable spatial snapping to normalize coordinate data and prevent redundant counts, while search segments allow fine-grained timing within a broader search session. Export functionality supports CSV generation and local storage persistence, enabling structured data retrieval and post-processing outside the device. Alert handling and synchronization packet preparation provide integration points for user notification and group-level coordination without exceeding Connect IQ sandbox limitations. The architecture emphasizes predictable memory usage, modular growth, and clean separation of search geometry, execution tracking, and analytics logic, making it a scalable foundation for future search-related extensions within the Leadalong system.

</details>

------------------

![Routes](https://github.com/user-attachments/assets/8e83d27d-d71e-4922-acce-ecd70c76de6a)

Monkey C compatibility across Garmin devices depends entirely on whether the device supports the Connect IQ platform and on the specific API level and capability profile that device exposes. Not all Garmin devices are equal in terms of memory, screen resolution, available sensors, background processing permissions, networking support, or storage limits. Even among Connect IQ–enabled devices, there are differences in available modules such as Communications, BluetoothLowEnergy, or advanced mapping interfaces. A module like Leadalong.Groups, which relies on multiuser coordination, route management, and potential phone-relayed synchronization, requires a device that supports modern Connect IQ API levels, has sufficient memory for state tracking, and ideally includes mapping and positioning capabilities suitable for outdoor navigation. Larger-screen, higher-tier handhelds and performance watches typically offer better support for complex UI rendering and persistent storage. Therefore, when targeting compatibility, the module must be configured in its manifest to include only devices that support the necessary APIs and have adequate memory and display capabilities for group tracking and race visualization.

```
Garmin Montana 700
Garmin Montana 700i
Garmin Montana 710
Garmin Montana 710i
Garmin Montana 760i
Garmin GPSMAP 67
Garmin GPSMAP 67i
Garmin Fenix 6 Series
Garmin Fenix 7 Series
Garmin Fenix 8 Series
Garmin Epix Gen 2
Garmin Epix Pro
Garmin Forerunner 255
Garmin Forerunner 265
Garmin Forerunner 955
Garmin Forerunner 965
Garmin Enduro 2
Garmin Instinct 2
Garmin Instinct 2X
Garmin Edge 540
Garmin Edge 840
Garmin Edge 1040
Garmin Edge 1050
Garmin Tread Series (Connect IQ compatible models)
```

------------------
https://sourceduty.com/
