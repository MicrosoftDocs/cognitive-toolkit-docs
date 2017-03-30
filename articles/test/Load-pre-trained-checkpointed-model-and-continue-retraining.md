You have a CNTK `trainer` object and save a checkpoint file.
  
    checkpoint_file = "mModel_checkpointed.dnn"
    trainer.save_checkpoint(checkpoint_file)

At a later time I load the `".dnn"` file as my model

    mymodel.load_model(checkpoint_file)

When you restart training and it would load the model but not the weights. To be able to restore training from the previously checkpointed model. You need to use [`trainer.restore_from_checkpoint`](https://cntk.ai/pythondocs/cntk.trainer.html?highlight=restore_from_checkpoint#cntk.trainer.Trainer.restore_from_checkpoint) instead, to recreate the trainer and learners. One important thing is that in your python script the order of network creation (not only structure! but the order in which you create your nodes) should stay the same at the point when you create a checkpoint and when you restore from it.

