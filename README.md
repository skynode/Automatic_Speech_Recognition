# Automatic-Speech-Recognition
End-to-end automatic speech recognition system implemented in TensorFlow.

## Recent Updates
- [x] **Support TensorFlow r1.0** (2017-02-24)
- [x] **Support dropout for dynamic rnn** (2017-03-11)
- [x] **Support running in shell file** (2017-03-11)
- [x] **Support evaluation every several training epoches automatically** (2017-03-11)
- [x] **Fix bugs for character-level automatic speech recognition** (2017-03-14)
- [x] **Improve some function apis for reusable** (2017-03-14)
- [x] **Add scaling for data preprocessing** (2017-03-15)
- [x] **Add reusable support for LibriSpeech training** (2017-03-15)
- [x] **Add simple n-gram model for random generation or statistical use** (2017-03-23)
- [x] **Improve some code for pre-processing and training** (2017-03-23)
- [x] **Replace TABs with blanks and add nist2wav converter script** (2017-04-20)
- [x] **Add some data preparation code** (2017-05-01)
- [x] **Add WSJ corpus standard preprocessing by s5 recipe** (2017-05-05)
- [x] **Restructuring of the project. Updated train.py for usage convinience** (2017-05-06)

## Recommendation
If you want to replace feed dict operation with Tensorflow multi-thread and fifoqueue input pipeline, you can refer to my repo [TensorFlow-Input-Pipeline](https://github.com/zzw922cn/TensorFlow-Input-Pipeline) for more example codes. My own practices prove that fifoqueue input pipeline would improve the training speed in some time.

If you want to look the history of speech recognition, I have collected the significant papers since 1981 in the ASR field. You can read awesome paper list in my repo [awesome-speech-recognition-papers](https://github.com/zzw922cn/awesome-speech-recognition-papers), all download links of papers are provided. I will update it every week to add new papers, including speech recognition, speech synthesis and language modelling. I hope that we won't miss any important papers in speech domain.

All my public repos will be updated in future, thanks for your stars!

## Install and Usage
Currently only python 2.7 is supported.

This project depends on scikit.audiolab, for which you need to have [libsndfile](http://www.mega-nerd.com/libsndfile/) installed in your system.
Clone the repository to your preferred directory and install the dependencies using:
<pre>
pip install -r requirements.txt
</pre>
To use, simply run the following command:
<pre>
python main/timit_train.py [-h] [--mode MODE] [--keep [KEEP]] [--nokeep]
                      [--level LEVEL] [--model MODEL] [--rnncell RNNCELL]
                      [--num_layer NUM_LAYER] [--activation ACTIVATION]
                      [--optimizer OPTIMIZER] [--batch_size BATCH_SIZE]
                      [--num_hidden NUM_HIDDEN] [--num_feature NUM_FEATURE]
                      [--num_classes NUM_CLASSES] [--num_epochs NUM_EPOCHS]
                      [--lr LR] [--dropout_prob DROPOUT_PROB]
                      [--grad_clip GRAD_CLIP] [--datadir DATADIR]
                      [--logdir LOGDIR]

optional arguments:
  -h, --help            show this help message and exit
  --mode MODE           set whether to train or test
  --keep [KEEP]         set whether to restore a model, when test mode, keep
                        should be set to True
  --nokeep
  --level LEVEL         set the task level, phn, cha, or seq2seq, seq2seq will
                        be supported soon
  --model MODEL         set the model to use, DBiRNN, BiRNN, ResNet..
  --rnncell RNNCELL     set the rnncell to use, rnn, gru, lstm...
  --num_layer NUM_LAYER
                        set the layers for rnn
  --activation ACTIVATION
                        set the activation to use, sigmoid, tanh, relu, elu...
  --optimizer OPTIMIZER
                        set the optimizer to use, sgd, adam...
  --batch_size BATCH_SIZE
                        set the batch size
  --num_hidden NUM_HIDDEN
                        set the hidden size of rnn cell
  --num_feature NUM_FEATURE
                        set the size of input feature
  --num_classes NUM_CLASSES
                        set the number of output classes
  --num_epochs NUM_EPOCHS
                        set the number of epochs
  --lr LR               set the learning rate
  --dropout_prob DROPOUT_PROB
                        set probability of dropout
  --grad_clip GRAD_CLIP
                        set the threshold of gradient clipping
  --datadir DATADIR     set the data root directory
  --logdir LOGDIR       set the log directory

</pre>
Instead of configuration in command line, you can also set the arguments above in [train.py](main/train.py) in practice.

Besides, you can also run `main/run.sh` for both training and testing simultaneously! See [run.sh](main/run.sh) for details.

## Performance
### PER based dynamic BLSTM on TIMIT database, with casual tuning because time it limited
![image](https://github.com/zzw922cn/Automatic_Speech_Recognition/blob/master/PER.png)

## Content
This is a powerful library for **automatic speech recognition**, it is implemented in TensorFlow and support training with CPU/GPU. This library contains followings models you can choose to train your own model:
* Data Pre-processing
* Acoustic Modeling
  * RNN
  * BRNN
  * LSTM
  * BLSTM
  * GRU
  * BGRU
  * Dynamic RNN
  * Deep Residual Network
  * Seq2Seq with attention decoder
  * etc.
* CTC Decoding
* Evaluation(Mapping some similar phonemes)  
* Saving or Restoring Model
* Mini-batch Training
* Training with GPU or CPU with TensorFlow
* Keeping logging of epoch time and error rate in disk

## Implementation Details

### Data preprocessing

#### TIMIT corpus

The original TIMIT database contains 6300 utterances, but we find the 'SA' audio files occurs many times, it will lead bad bias for our speech recognition system. Therefore, we removed the all 'SA' files from the original dataset and attain the new TIMIT dataset, which contains only 5040 utterances including 3696 standard training set and 1344 test set.

Automatic Speech Recognition transcribes a raw audio file into character sequences; the preprocessing stage converts a raw audio file into feature vectors of several frames. We first split each audio file into 20ms Hamming windows with an overlap of 10ms, and then calculate the 12 mel frequency ceptral coefficients, appending an energy variable to each frame. This results in a vector of length 13. We then calculate the delta coefficients and delta-delta coefficients, attaining a total of 39 coefficients for each frame. In other words, each audio file is split into frames using the Hamming windows function, and each frame is extracted to a feature vector of length 39 (to attain a feature vector of different length, modify the settings in the file [timit\_preprocess.py](https://github.com/zzw922cn/Automatic-Speech-Recognition/blob/master/src/feature/timit_preprocess.py).

In folder data/mfcc, each file is a feature matrix with size timeLength\*39 of one audio file; in folder data/label, each file is a label vector according to the mfcc file.

If you want to set your own data preprocessing, you can edit [calcmfcc.py](https://github.com/zzw922cn/Automatic-Speech-Recognition/blob/master/src/feature/calcmfcc.py) or [timit\_preprocess.py](https://github.com/zzw922cn/Automatic-Speech-Recognition/blob/master/src/feature/timit_preprocess.py).

Since the original TIMIT dataset contains 61 phonemes, we use 61 phonemes for training and evaluation, but when scoring, we mappd the 61 phonemes into 39 phonemes for better performance. We do this mapping according to the paper [Speaker-independent phone recognition using hidden Markov models](http://repository.cmu.edu/cgi/viewcontent.cgi?article=2768&context=compsci). The mapping details are as follows:

| original phoneme(s) | mapped into phoneme |
| :------------------  | :-------------------: |
| ux | uw |
| axr | er |
| em | m |
| nx, n  | en |
| eng | ng |
| hv | hh |
| cl, bcl, dcl, gcl, epi, h#, kcl, pau, pcl, tcl, vcl | sil |
| l | el |
| zh | sh |
| aa | ao |
| ix | ih |
| ax | ah | 
 

#### LibriSpeech corpus

TODO

#### Wall Street Journal corpus

TODO

### Core Features
+ dynamic RNN(GRU, LSTM)
+ Residual Network(Deep CNN)
+ CTC Decoding
+ TIMIT Phoneme Edit Distance(PER)

## Future Work
- [ ] Add Attention Mechanism
- [ ] Add more efficient dynamic computation graph without padding
- [ ] List experimental results 
- [ ] Implement more ASR models following newest investigations 
- [ ] Provide fast TensorFlow Input Pipeline 

## License
MIT

## Contact Us
If this program is helpful to you, please give us a **star or fork** to encourage us to keep updating. Thank you! Besides, any issues or pulls are appreciated.

For any questions, welcome to send email to :**zzw922cn@gmail.com**. 

Collaborators: 

[zzw922cn](https://github.com/zzw922cn)

[hiteshpaul](https://github.com/hiteshpaul)

[xxxxyzt](https://github.com/xxxxyzt)
