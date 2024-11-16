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



config.SIMULATOR是个类，里面保存所有sim信息，作为这个类的属性

config.SIMULATOR.AGENT_0.SENSORS保存所有sim的名称



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





#### 疑问

```
 self.config.TASK_CONFIG.DATASET.ROLES = ["guide"]
 

```

