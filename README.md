![GPS](https://github.com/user-attachments/assets/bf593fb7-4520-494a-968c-bb63d8840c9f)

Leadalong is designed as a structured multiuser coordination framework built specifically for Garmin devices that support the Connect IQ platform. Its purpose is to transform traditionally single-user GPS navigation into a synchronized group-based experience where multiple participants can operate under a shared state model. Rather than simply broadcasting positions, Leadalong introduces organized constructs such as groups, shared waypoints, shared routes, race overlays, role assignments, and spread monitoring. The architecture is modular, beginning with the Groups submodule as the coordination core, but structured so additional submodules can later extend functionality into areas like synchronized navigation cues, shared objective tracking, cooperative task completion, or real-time expedition management. It is intentionally designed around Connect IQ’s sandbox limitations, meaning it prioritizes lightweight state synchronization, efficient data structures, and periodic update logic rather than heavy real-time streaming.

From a functional perspective, Leadalong enables both cooperative and competitive outdoor dynamics. Cooperative use cases include guided hikes where a leader distributes route updates and monitors group cohesion, cycling teams maintaining pace alignment, or expedition teams tracking spatial separation thresholds for safety. Competitive use cases include dual and triple route races where participants travel in different modes—walking, running, cycling, vehicle, or even aircraft—and compare progression along a shared route. The module tracks participation state, progress percentages, timestamps, and positional updates while remaining device-aware and memory-conscious. By combining structured group state management with extensible routing and race logic, Leadalong serves as a programmable coordination layer that enhances Garmin’s navigation ecosystem without modifying core firmware, making it both scalable and compliant with Connect IQ constraints.

------------------

<details>
<summary>Groups</summary>

The Leadalong.Groups module can be expanded to support structured shared navigation objects and competitive multi-mode racing logic while staying within Connect IQ constraints. The group_waypoint, group_route, and group_track capabilities extend the shared state model so that navigation artifacts are distributed to members and synchronized periodically. Waypoints represent discrete geographic targets, routes represent ordered waypoint paths, and group tracking maintains a lightweight position table that can be used to compute spread, pacing, and compliance relative to leader-defined objectives. Because Connect IQ networking is constrained, synchronization would typically occur through paired phone relay or periodic peer messaging, with compressed state payloads and timestamp validation to prevent stale updates.

The racing functions introduce a competitive overlay that compares distance progress, elapsed time, and speed across different movement modes. double_route_race supports two racers operating in potentially different travel modes, while triple_route_race extends the logic to three participants. Each racer maintains a structured race profile that includes mode classification, route reference, start time, checkpoint index, and completion state. The comparison engine can compute percent completion along a shared route, estimated finish time, and leader delta. The code below expands the GroupsManager class with the requested functions and supporting data structures in Monkey C prototype form.

```
using Toybox.Application;
using Toybox.Communications;
using Toybox.Position;
using Toybox.Time;
using Toybox.Math;
using Toybox.Lang;

module Leadalong {

    module Groups {

        class GroupsManager {

            var groupId;
            var members;
            var leaderId;
            var sharedWaypoints;
            var sharedRoutes;
            var memberPositions;
            var activeRaces;

            function initialize() {
                members = [];
                sharedWaypoints = [];
                sharedRoutes = [];
                memberPositions = {};
                activeRaces = [];
            }

            function group_waypoint(name as String, lat as Float, lon as Float) {
                var waypoint = {
                    :name => name,
                    :lat  => lat,
                    :lon  => lon,
                    :timestamp => Time.now()
                };
                sharedWaypoints.add(waypoint);
                return waypoint;
            }

            function group_route(name as String, routePoints as Array) {
                var route = {
                    :name => name,
                    :points => routePoints,
                    :created => Time.now()
                };
                sharedRoutes.add(route);
                return route;
            }

            function group_track(memberId as String, lat as Float, lon as Float) {
                memberPositions[memberId] = {
                    :lat => lat,
                    :lon => lon,
                    :timestamp => Time.now()
                };
            }

            function get_group_track(memberId as String) {
                return memberPositions[memberId];
            }

            function double_route_race(racerA as String, racerB as String, routeName as String, modeA as String, modeB as String) {

                var race = {
                    :type => "double",
                    :route => routeName,
                    :racers => [
                        { :id => racerA, :mode => modeA, :start => Time.now(), :progress => 0.0 },
                        { :id => racerB, :mode => modeB, :start => Time.now(), :progress => 0.0 }
                    ],
                    :status => "active"
                };

                activeRaces.add(race);
                return race;
            }

            function triple_route_race(racerA as String, racerB as String, racerC as String, routeName as String, modeA as String, modeB as String, modeC as String) {

                var race = {
                    :type => "triple",
                    :route => routeName,
                    :racers => [
                        { :id => racerA, :mode => modeA, :start => Time.now(), :progress => 0.0 },
                        { :id => racerB, :mode => modeB, :start => Time.now(), :progress => 0.0 },
                        { :id => racerC, :mode => modeC, :start => Time.now(), :progress => 0.0 }
                    ],
                    :status => "active"
                };

                activeRaces.add(race);
                return race;
            }

            function update_race_progress(racerId as String, progress as Float) {
                for (var i = 0; i < activeRaces.size(); i++) {
                    var race = activeRaces[i];
                    for (var j = 0; j < race[:racers].size(); j++) {
                        if (race[:racers][j][:id] == racerId) {
                            race[:racers][j][:progress] = progress;
                        }
                    }
                }
            }

            function get_race_status(index as Number) {
                if (index < activeRaces.size()) {
                    return activeRaces[index];
                }
                return null;
            }

            function end_race(index as Number) {
                if (index < activeRaces.size()) {
                    activeRaces[index][:status] = "completed";
                }
            }
        }
    }
}
```

</details>

------------------

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
