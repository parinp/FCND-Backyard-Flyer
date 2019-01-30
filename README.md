# Backyard Flyer project

## Objective: Commanding Quadcopter

The full python code is written in the file __backyard_flyer.py__.
The objective of this project is to get us students familiar with event driven programming as well as commanding the drone to fly a fixed square path.

This will be achieved through **callbacks**, where if a value associated with **velocity**, **local position** and **vehicle state** changes, it prompts it associated function to recalculate its conditions.  We will be focusing on local position callback as it is the function that will allow the drone to fly towards its next target position.

```python
  self.register_callback(MsgID.LOCAL_POSITION, self.local_position_callback)
  self.register_callback(MsgID.LOCAL_VELOCITY, self.velocity_callback)
  self.register_callback(MsgID.STATE, self.state_callback)

```

### Local Position Callback

This function is responsible for determining whether the quadrotor's current position has reached its targeted waypoint in order to continue onwards onto the next waypoint or land.

```python
    def local_position_callback(self):
       
        if self.flight_state == States.TAKEOFF:
            altitude_current = -1.0*self.local_position[2]
            if altitude_current > 0.95*self.target_position[2]:
                self.calculate_box()
                self.waypoint_transition()
        elif self.flight_state == States.WAYPOINT:
            if np.linalg.norm(self.local_position[0:2] - self.target_position[0:2]) < 1:
                if len(self.all_waypoints) > 0:
                    self.waypoint_transition()
                else:
                     if all(self.local_velocity[0:2])<0.01:
                            self.landing_transition()
```

### Velocity Callback

This function is used in order to disarm the quadrotor.  When the local position of the quadrotor is within the goal position, and also the altitude is close to 0 (ground), then this function will disarm the quadrotor.

```python
    def velocity_callback(self):

        if self.flight_state == States.LANDING:
            if np.linalg.norm(self.global_position[0:2]-self.global_home[0:2]) < 1.0:
                if abs(self.local_position[2])<0.01:
                    self.disarming_transition() 
```

### License

This project is licensed under the [MIT License](LICENSE)
