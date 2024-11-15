# Discrete-Continuous-VLN

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
config.merge_from_file(run_CMA.yaml)
config.TASK_CONFIG=get_task_config(habitat_extensions/config/vlnce_task.yaml)
```



```
    # TRAINER_NAME: schedulesampler-CMA
    trainer_init = baseline_registry.get_trainer(config.TRAINER_NAME)
    assert trainer_init is not None, f"{config.TRAINER_NAME} is not supported"
    trainer = trainer_init(config)
```

