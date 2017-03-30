To continue training from a previously saved snapshot of the model you need to do two things:
* Set keepCheckPointFiles to true in your config file so that check point files will not be deleted.
* Copy the model file and the corresponding check point files to a new folder and point your continued training to that folder.
 