# Discrete-Continuous-VLN

这篇文章并没有提到low level action的具体设定，看来应该是根据预测出的waypoint位置自动得到需要执行的low level action



可以修改config.EVAL.SAVE_RESULTS=False，不保存evaluation result



[Data · VLN-CE](https://jacobkrantz.github.io/vlnce/data)

```
deserialized ={
'episodes': [{...}, {...}, {...}, {...}, {...}, {...}, {...}, {...}, {...}, ...],
'instruction_vocab': {'word_list': [...], 'word2idx_dict': {...}, 'stoi': {...}, 'itos': [...], 'num_vocab': 2504, 'UNK_INDEX': 1, 'PAD_INDEX': 0}
}

```



```bash
# # EVALUATION
flag="--exp_name cont-cwp-cma-ori
      --run-type eval
      --exp-config run_CMA.yaml
      SIMULATOR_GPU_IDS [0]
      TORCH_GPU_ID 0
      TORCH_GPU_IDS [0]
      EVAL.SPLIT val_unseen
      EVAL_CKPT_PATH_DIR logs/checkpoints/cont-cwp-cma-ori/cma_ckpt_best.pth
      "
python run.py $flag
```



```
parser.add_argument('--local_rank', type=int, default=0, help="local gpu id")
_C.VIDEO_OPTION = []  # options: "disk", "tensorboard"
config.merge_from_file(run_CMA.yaml)
config.TASK_CONFIG=get_task_config(habitat_extensions/config/vlnce_task.yaml)
```



```
    # TRAINER_NAME: schedulesampler-CMA
    trainer_init = baseline_registry.get_trainer(config.TRAINER_NAME)
    assert trainer_init is not None, f"{config.TRAINER_NAME} is not supported"
    trainer = trainer_init(config)
```



```
world_size = self.config.GPU_NUMBERS

self.config.TASK_CONFIG.DATASET.LANGUAGES = self.config.EVAL.LANGUAGES
self.config.TASK_CONFIG.DATASET.SPLIT = self.config.EVAL.SPLIT
self.config.TASK_CONFIG.TASK.NDTW.SPLIT = self.config.EVAL.SPLIT
self.config.TASK_CONFIG.TASK.SDTW.SPLIT = self.config.EVAL.SPLIT

resize_config = self.config.RL.POLICY.OBS_TRANSFORMS.RESIZER_PER_SENSOR.SIZES
_C.RL.POLICY.OBS_TRANSFORMS.RESIZER_PER_SENSOR = CN()
_C.RL.POLICY.OBS_TRANSFORMS.RESIZER_PER_SENSOR.SIZES = [
    ("rgb", (224, 224)),
    ("depth", (256, 256)),
]

config = self.config.TASK_CONFIG
```



感觉这段代码写的有问题，只适用于num_views=12的情况

还是说math.pi / 6就是固定的，vfov不变？

```
camera_orientations = get_camera_orientations(12)

def get_camera_orientations(num_views):
    assert isinstance(num_views, int)
    base_angle_deg = 360 / num_views
    base_angle_rad = math.pi / 6
    orient_dict = {}
    for k in range(1,num_views):
        orient_dict[str(base_angle_deg*k)] = [0.0, base_angle_rad*k, 0.0]
    return orient_dict
```



```
camera_orientations = {'30.0': [0.0, 0.5235987755982988, 0.0], '60.0': [0.0, 1.0471975511965976, 0.0], '90.0': [0.0, 1.5707963267948966, 0.0], '120.0': [0.0, 2.0943951023931953, 0.0], '150.0': [0.0, 2.617993877991494, 0.0], '180.0': [0.0, 3.141592653589793, 0.0], '210.0': [0.0, 3.665191429188092, 0.0], '240.0': [0.0, 4.1887902047863905, 0.0], '270.0': [0.0, 4.71238898038469, 0.0], '300.0': [0.0, 5.235987755982988, 0.0], '330.0': [0.0, 5.759586531581287, 0.0]}
```

config.SIMULATOR是个类，里面保存所有sim信息，作为这个类的属性

self.config.SENSORS=config.SIMULATOR.AGENT_0.SENSORS保存所有sim的名称

```
self.config.RL.POLICY.OBS_TRANSFORMS.RESIZER_PER_SENSOR.SIZES = resize_config
```







```
self.traj = self.collect_val_traj()

# self.config.TASK_CONFIG.TASK.NDTW.GT_PATH = 
# data/datasets/R2R_VLNCE_v1-2_preprocessed/{split}/{split}_gt.json.gz
    def collect_val_traj(self):
        from habitat_extensions.task import ALL_ROLES_MASK, RxRVLNCEDatasetV1
        trajectories = defaultdict(list)
        split = self.config.TASK_CONFIG.DATASET.SPLIT

        if 'rxr' in self.config.BASE_TASK_CONFIG_PATH:
			xxx
        else:
            with gzip.open(
                self.config.TASK_CONFIG.TASK.NDTW.GT_PATH.format(
                    split=split)
            ) as f:
                gt_data = json.load(f)

        self.gt_data = gt_data

        trajectories = gt_data
        self.trajectories = gt_data
        trajectories = list(trajectories.keys())[self.config.local_rank::self.config.GPU_NUMBERS]

        return trajectories
```



```
self.gt_data = {'991': {'locations': [...], 'forward_steps': 39, 'actions': [...]}, '1247': {'locations': [...], 'forward_steps': 29, 'actions': [...]}, '1019': {'locations': [...], 'forward_steps': 32, 'actions': [...]}, '338': {'locations': [...], 'forward_steps': 27, 'actions': [...]}, '1487': {'locations': [...], 'forward_steps': 39, 'actions': [...]}, '259': {'locations': [...], 'forward_steps': 22, 'actions': [...]}, '1711': {'locations': [...], 'forward_steps': 27, 'actions': [...]}, '1122': {'locations': [...], 'forward_steps': 34, 'actions': [...]}, '1018': {'locations': [...], 'forward_steps': 32, 'actions': [...]}, '1369': {'locations': [...], 'forward_steps': 31, 'actions': [...]}, '1280': {'locations': [...], 'forward_steps': 46, 'actions': [...]}, '406': {'locations': [...], 'forward_steps': 42, 'actions': [...]}, '1121': {'locations': [...], 'forward_steps': 34, 'actions': [...]}, '1125': {'locations': [...], 'forward_steps': 26, 'actions': [...]}, ...}

self.traj = self.collect_val_traj()=['991', '1247', '1019', '338', '1487', '259', '1711', '1122', '1018', '1369', '1280', '406', '1121', '1125', ...]
```





进入self._eval_checkpoint()



首先进入construct_envs

```
        # config.ENV_NAME='VLNCEDaggerEnv'
		# 得到VLNCEDaggerEnv的环境类
        envs = construct_envs(
            config, get_env_class(config.ENV_NAME),
            auto_reset_done=False,
            episodes_allowed=self.traj
        )
```

在construct_envs中

VLNCEDatasetV1.dataset

construct_envs结束



```
        obs_transforms = get_active_obs_transforms(config)
        # obs_transforms = [CenterCropperPerSensor()]
```





#### 疑问

```
 self.config.TASK_CONFIG.DATASET.ROLES = ["guide"]
 

```

