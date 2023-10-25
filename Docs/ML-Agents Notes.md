# Machine Learning in Unity (ML-Agents)

## Agents

An agent is a game object that can run AI both for training and for playing. It usually associates with a C# script.

An example/tutorial video on YouTube: [MoveToGoal](https://www.youtube.com/watch?v=zPFU30tbyKs&list=PLf-b3RpcHtuuEfWD5hbB2BGtgFpl3xHk5&index=18&t=745s&pp=gAQBiAQB)

### Agent C# Script

Example script:

 ```c#
 using UnityEngine;
 using Unity.MLAgents;
 using Unity.MLAgents.Sensors;
 
 public class MoveToGoalAgent : Agent
 {
     [SerializeField] private Transform targetTransform;
     [SerializeField] private Material winMaterial;
     [SerializeField] private Material loseMaterial;
     [SerializeField] private MeshRenderer floorMeshRenderer;
     
     public override void OnEpisodeBegin()
     {
         transform.localPosition = new Vector3(Random.Range(-3f, 1f), 0, Random.Range(-2f, 2f));
         targetTransform.localPosition = new Vector3(Random.Range(2.4f, 5f), 0, Random.Range(-2f, 2f));
     }
     
     public override void CollectObservations(VectorSensor sensor)
     {
         sensor.AddObservation(transform.localPosition);
         sensor.AddObservation(targetTransform.localPosition);
     }
     
     public override void OnActionReceived(ActionBuffers actions)
     {
         // Debug.Log(actions.DiscreteActions[0]);
         var moveX = actions.ContinuousActions[0];
         var moveZ = actions.ContinuousActions[1];
         var moveSpeed = 3f;
         
         transform.localPosition += new Vector3(moveX, 0, moveZ) * Time.deltaTime * moveSpeed;
     }
     
     public override void Heuristic(in ActionBuffers actionsOut)
     {
         var continuousActions = actionsOut.ContinuousActions;
         
         continuousActions[0] = Input.GetAxisRaw("Horizontal");
         continuousActions[1] = Input.GetAxisRaw("Vertical");
     }
     
     private void OnTriggerEnter(Collider other)
     {
         if (other.TryGetComponent<Goal>(out Goal goal))
         {
             SetReward(1f);
             floorMeshRenderer.material = winMaterial;
             EndEpisode();
         }
         if (other.TryGetComponent<Wall>(out Wall wall))
         {
             SetReward(-1f);
             floorMeshRenderer.material = loseMaterial;
             EndEpisode();
         }
     }
 }
 ```

### Agent Game Object Components

#### Behavior Parameters

- `Vector Action`

  - `Space Type`

    - Discrete: action value will be integers
    - Continuous: action value will be floats

  - `Branches Size` (Discrete) & `Space Size` (Continuous)

    - ```c#
      namespace Unity.MLAgents.Actuators
      {
          public readonly struct ActionBuffers
          {
              public ActionSegment<float> ContinuousActions { get; }
              public ActionSegment<int> DiscreteActions { get; }
          }
      }
      ```

    - `ActionSegment` is like an array

    - For example, `Space Type: Discrete, Branches Size: 2, Brach 0 Size: 2, Branch 1 Size: 3` could means: action values for branch 0 will be 0 or 1, action values for branch 1 will be 0, 1, or 2. This can be used to represent a simple car AI, then the branch 0 could refer to accelerating and breaking, the branch 1 could refer to turning left, turning right, and going straight.

- `Vector Observation`

  - `Space Size`: in the example script, the sensor is fed with the agent's position and also the target's position. In 3D, the `Space Size` will be 6.
  - `Stacked Vectors`: used when you need the AI to have some sort of memory. For example, 2 means AI will take one observation and also the last one and uses both of those to make its decision.

- `Behavior Type`

  - `Default`: if no model is selected and no ML-Agents is running, then Heuristic is used automatically.
  - `Heuristic Only`
  - `Inference Only`


#### Decision Requester

Request a decision every certain amount of time and then take actions.

#### Agent (Script)

- `Max Step`: by default it runs 50 steps per second.