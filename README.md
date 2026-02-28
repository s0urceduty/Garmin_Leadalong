![GPS](https://github.com/user-attachments/assets/bf593fb7-4520-494a-968c-bb63d8840c9f)

Leadalong is Monkey C module designed as a structured multiuser coordination framework built specifically for Garmin devices that support the Connect IQ platform. Its purpose is to transform traditionally single-user GPS navigation into a synchronized group-based experience where multiple participants can operate under a shared state model. Rather than simply broadcasting positions, Leadalong introduces organized constructs such as groups, shared waypoints, shared routes, race overlays, role assignments, and spread monitoring. The architecture is modular, beginning with the Groups submodule as the coordination core, but structured so additional submodules can later extend functionality into areas like synchronized navigation cues, shared objective tracking, cooperative task completion, or real-time expedition management. It is intentionally designed around Connect IQ’s sandbox limitations, meaning it prioritizes lightweight state synchronization, efficient data structures, and periodic update logic rather than heavy real-time streaming.

From a functional perspective, Leadalong enables both cooperative and competitive outdoor dynamics. Cooperative use cases include guided hikes where a leader distributes route updates and monitors group cohesion, cycling teams maintaining pace alignment, or expedition teams tracking spatial separation thresholds for safety. Competitive use cases include dual and triple route races where participants travel in different modes—walking, running, cycling, vehicle, or even aircraft—and compare progression along a shared route. The module tracks participation state, progress percentages, timestamps, and positional updates while remaining device-aware and memory-conscious. By combining structured group state management with extensible routing and race logic, Leadalong serves as a programmable coordination layer that enhances Garmin’s navigation ecosystem without modifying core firmware, making it both scalable and compliant with Connect IQ constraints.

Submodules:
------------------

<details>
<summary>Groups</summary>

Leadalong.Groups is 30 advanced multiuser navigation and racing functions. This consolidated structure establishes a scalable architecture for group lifecycle management, role control, messaging, shared navigation objects, spread analytics, synchronization logic, and structured race handling. The design keeps state centralized inside a GroupsManager class while organizing shared waypoints, routes, member tracking, and active race objects in dedicated collections. This keeps memory usage predictable and makes the module extensible for future submodules such as synchronized checkpoint validation, leaderboard rendering, adaptive pacing analysis, or distributed event triggers.

Because Connect IQ runs in a sandboxed environment with limited execution time and memory constraints, this prototype keeps networking and heavy computation abstracted as placeholders. Real-world deployment would require careful API-level targeting and device capability filtering in the manifest. The combined module below represents a structured baseline suitable for refinement into a production-ready Connect IQ application for compatible Garmin devices.

```
using Toybox.Application;
using Toybox.Activity;
using Toybox.Attention;
using Toybox.Background;
using Toybox.BluetoothLowEnergy;
using Toybox.Communications;
using Toybox.Fit;
using Toybox.Graphics;
using Toybox.Lang;
using Toybox.Math;
using Toybox.Position;
using Toybox.Sensor;
using Toybox.SensorHistory;
using Toybox.Storage;
using Toybox.System;
using Toybox.Time;

module Leadalong {

    module Groups {

        class GroupsManager {

            var groupId;
            var members;
            var leaderId;
            var sharedWaypoints;
            var sharedRoutes;
            var memberPositions;
            var groupSettings;
            var activeRaces;

            function initialize() {
                members = [];
                sharedWaypoints = [];
                sharedRoutes = [];
                memberPositions = {};
                groupSettings = {};
                activeRaces = [];
            }

            function createGroup(id as String) { groupId = id; }

            function joinGroup(id as String) { groupId = id; }

            function leaveGroup() { groupId = null; }

            function assignLeader(memberId as String) { leaderId = memberId; }

            function getLeader() { return leaderId; }

            function addMember(memberId as String) { members.add(memberId); }

            function removeMember(memberId as String) {
                var idx = members.indexOf(memberId);
                if (idx != null) { members.remove(idx); }
            }

            function listMembers() { return members; }

            function broadcastMessage(message as String) { }

            function sendDirectMessage(memberId as String, message as String) { }

            function publishWaypoint(lat as Float, lon as Float) {
                sharedWaypoints.add({:lat=>lat,:lon=>lon,:timestamp=>Time.now()});
            }

            function getSharedWaypoints() { return sharedWaypoints; }

            function clearSharedWaypoints() { sharedWaypoints.clear(); }

            function updateMemberPosition(memberId as String, lat as Float, lon as Float) {
                memberPositions[memberId] = {:lat=>lat,:lon=>lon,:timestamp=>Time.now()};
            }

            function getMemberPosition(memberId as String) {
                return memberPositions[memberId];
            }

            function calculateGroupSpread() { return 0; }

            function setSpreadAlertThreshold(distance as Float) {
                groupSettings[:spreadThreshold] = distance;
            }

            function checkSpreadAlert() { return false; }

            function syncGroupState() { }

            function acknowledgeSync(memberId as String) { }

            function setGroupSetting(key as String, value) {
                groupSettings[key] = value;
            }

            function getGroupSetting(key as String) {
                return groupSettings[key];
            }

            function dissolveGroup() {
                members.clear();
                sharedWaypoints.clear();
                sharedRoutes.clear();
                memberPositions.clear();
                groupId = null;
                leaderId = null;
            }

            function getGroupId() { return groupId; }

            function isMember(memberId as String) {
                return members.indexOf(memberId) != null;
            }

            function group_waypoint(name as String, lat as Float, lon as Float) {
                var waypoint = {
                    :name=>name,
                    :lat=>lat,
                    :lon=>lon,
                    :timestamp=>Time.now()
                };
                sharedWaypoints.add(waypoint);
                return waypoint;
            }

            function group_route(name as String, routePoints as Array) {
                var route = {
                    :name=>name,
                    :points=>routePoints,
                    :created=>Time.now()
                };
                sharedRoutes.add(route);
                return route;
            }

            function group_track(memberId as String, lat as Float, lon as Float) {
                memberPositions[memberId] = {
                    :lat=>lat,
                    :lon=>lon,
                    :timestamp=>Time.now()
                };
            }

            function double_route_race(racerA as String, racerB as String, routeName as String, modeA as String, modeB as String) {

                var race = {
                    :type=>"double",
                    :route=>routeName,
                    :racers=>[
                        {:id=>racerA,:mode=>modeA,:start=>Time.now(),:progress=>0.0},
                        {:id=>racerB,:mode=>modeB,:start=>Time.now(),:progress=>0.0}
                    ],
                    :status=>"active"
                };

                activeRaces.add(race);
                return race;
            }

            function triple_route_race(racerA as String, racerB as String, racerC as String, routeName as String, modeA as String, modeB as String, modeC as String) {

                var race = {
                    :type=>"triple",
                    :route=>routeName,
                    :racers=>[
                        {:id=>racerA,:mode=>modeA,:start=>Time.now(),:progress=>0.0},
                        {:id=>racerB,:mode=>modeB,:start=>Time.now(),:progress=>0.0},
                        {:id=>racerC,:mode=>modeC,:start=>Time.now(),:progress=>0.0}
                    ],
                    :status=>"active"
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

<img width="1200" height="400" alt="Bikes" src="https://github.com/user-attachments/assets/109c6dd9-ba7e-41c7-bea3-cddeb2657241" />

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
