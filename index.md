---
title:
feature_text: |
  ## Welcome to Zhao Mingxin's Homepage
  A blog recording my daily thoughts and ideas.
feature_image: "https://unsplash.it/1300/400?image=733"
excerpt: "Alembic is a starting point for [Jekyll](https://jekyllrb.com/) projects. Rather than starting from scratch, this boilerplate is designed to get the ball rolling immediately. Install it, configure it, tweak it, push it."
---
<h2 align="center">
记录感想，分享知识
</h2>
{% highlight python linenos %}
import sys
import cPickle as pickle

from imagernn.data_provider import getDataProvider
from imagernn.solver import Solver
from imagernn.imagernn_utils import decodeGenerator, eval_split

def preProBuildWordVocab(sentence_iterator, word_count_threshold):
  # count up all word counts so that we can threshold
  # this shouldnt be too expensive of an operation
  print 'preprocessing word counts and creating vocab based on word count threshold %d' % (word_count_threshold, )
  t0 = time.time()
  word_counts = {}
  nsents = 0
  for sent in sentence_iterator:
    nsents += 1
    for w in sent['tokens']:
      word_counts[w] = word_counts.get(w, 0) + 1
  vocab = [w for w in word_counts if word_counts[w] >= word_count_threshold]
  print 'filtered words from %d to %d in %.2fs' % (len(word_counts), len(vocab), time.time() - t0)

  # with K distinct words:
  # - there are K+1 possible inputs (START token and all the words)
  # - there are K+1 possible outputs (END token and all the words)
  # we use ixtoword to take predicted indeces and map them to words for output visualization
  # we use wordtoix to take raw words and get their index in word vector matrix
  ixtoword = {}
  ixtoword[0] = '.'  # period at the end of the sentence. make first dimension be end token
  wordtoix = {}
  wordtoix['#START#'] = 0 # make first vector be the start token
  ix = 1
  for w in vocab:
    wordtoix[w] = ix
    ixtoword[ix] = w
    ix += 1
{% endhighlight %}