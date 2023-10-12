# Karting-RL Model Training

*Updated on 2023.10.12.*

- [Environment Preparation](#Environment Preparation)
- [Model Training](#Model Training)
- [References](#References)

## Environment Preparation

On Windows:

- [Python Setup](#Python Setup)
- [Unity Setup](#Unity Setup)

### Python Setup

We recommend using [Anaconda](https://www.anaconda.com/download) to set up Python environment:

1. Start a new Anaconda Prompt or Anaconda Powershell Prompt.

2. Create a new virtual environment with Python 3.8:

   ```powershell
   conda create --name mlagents python=3.8
   ```
   
3. Activate the `mlagents` environment:

   ```powershell
   conda activate mlagents
   ```

4. Install PyTorch:

   ```powershell
   pip install torch~=1.7.1 -f https://download.pytorch.org/whl/torch_stable.html
   ```

5. Install `mlagents` Python package and other dependencies:

   ```powershell
   pip install mlagents==0.30.0 protobuf==3.20 six
   ```

6. Run `mlagents-learn --help` to make sure the installation is successful. The "CUDA initialization" warning can be ignored.

If you prefer not to use Anaconda, just install above packages in your Python 3.8 with the correct order and versions.

### Unity Setup

1. Download and install [Unity](https://unity.com/download). Recommended editor version: 2021.3 (LTS) or 2022.3 (LTS).
2. Open the cloned Karting-RL project in the Unity Editor.
3. Open **Window** ↦ **Package Manager**, make sure the `ML Agents` Unity package is installed.

## Model Training

- [Load Training Scene](#Load Training Scene)
- [Adjust Training Configurations (Optional)](#Adjust Training Configurations (Optional))
- [Start Training Session](#Start Training Session)
- [Visualize Training Results](#Visualize Training Results)
- [Acquire Trained Model](#Acquire Trained Model)

### Load Training Scene

1. Open the Karting-RL project in the Unity Editor.

2. In the **Project** window, go to the `Assets/Karting/Scenes/MLTraining` folder, open `KartClassic_Training.unity`.

### Adjust Training Configurations (Optional)

Model training configuration file location: `Karting-RL\Assets\Karting\Prefabs\AI\kart_trainer_config.yaml`.

Refer to [Training Configuration File](https://unity-technologies.github.io/ml-agents/Training-Configuration-File/) for detailed explanations.

### Start Training Session

1. Open a new Anaconda Prompt or Anaconda Powershell Prompt.

2. Go to the `Karting-RL\Assets\Karting\Prefabs\AI` folder.

3. Activate the `mlagents` environment:

   ```powershell
   conda activate mlagents
   ```

4. Start a training session:

   ```powershell
   mlagents-learn kart_trainer_config.yaml --run-id=KartTest
   ```

   - `kart_trainer_config.yaml` is the path to the training configuration file.
   - `run-id` is a unique name for this training session.
   - Add `--resume` after specifying an existing `run-id` if you want to resume training from the previous session.
   - Add `--force` after specifying an existing `run-id` if you want to override the trained model of the previous session.

   If `mlagents-learn` runs successfully, the command line will display this:

   > [INFO] Listening on port 5004. Start training by pressing the Play button in the Unity Editor.

   Press the Play button in the Unity Editor to start training.

### Visualize Training Results

Once the training starts, a `results` folder will be generated and updated as the training progresses. You can visualize training results by using TensorBoard:

```powershell
tensorboard --logdir results
# "results" is the path to the results folder
```

Then navigate to `localhost:6006` in your browser to view the TensorBoard summary statistics.

### Acquire Trained Model

If the training session ends or is interrupted, the model file `.onnx` will be saved in the `results` folder. Checkpoints are also saved as models.

## References

[Unity ML-Agents Toolkit Documentation](https://unity-technologies.github.io/ml-agents/)

[Unity ML-Agents GitHub](https://github.com/Unity-Technologies/ml-agents)

[Unity Karting Microgame](https://learn.unity.com/project/karting-template)