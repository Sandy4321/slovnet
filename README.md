
<img src="https://github.com/natasha/natasha-logos/blob/master/slovnet.svg">

[![Build Status](https://travis-ci.org/natasha/slovnet.svg?branch=master)](https://travis-ci.org/natasha/slovnet)

SlovNet is a Python library for deep-learning based NLP modeling for Russian language. Library is integrated with other <a href="https://github.com/natasha/">Natasha</a> projects: <a href="https://github.com/natasha/nerus">large NER corpus</a> and <a href="https://github.com/natasha/navec">compact Russian embeddings</a>. SlovNet provides high quality practical model for Russian NER, it is 1-2% worse than current BERT SOTA by DeepPavlov but 60 times smaller in size (~30 MB) and works fast on CPU (~30 news articles/sec), see <a href="#evaluation">evaluation section</a> for more.

</table>

## Install

During inference `slovnet` depends only on `numpy`. Library supports Python 2.7+, 3.4+ и PyPy 3. PyPy 2 is excluded since it is hard to install `numpy` for PyPy 2.

```bash
$ pip install slovnet
```

## Usage

Download <a href="https://github.com/natasha/navec#downloads">news Navec embeddings</a> and <a href="#downloads">SlovNet news NER model</a>:

```python
>>> from navec import Navec
>>> from slovnet import NERTagger
>>> from ipymarkup import show_ascii_markup

>>> text = 'Европейский союз добавил в санкционный список девять политических деятелей из самопровозглашенных республик Донбасса — Донецкой народной республики (ДНР) и Луганской народной республики (ЛНР) — в связи с прошедшими там выборами. Об этом говорится в документе, опубликованном в официальном журнале Евросоюза. В новом списке фигурирует Леонид Пасечник, который по итогам выборов стал главой ЛНР. Помимо него там присутствуют Владимир Бидевка и Денис Мирошниченко, председатели законодательных органов ДНР и ЛНР, а также Ольга Позднякова и Елена Кравченко, председатели ЦИК обеих республик. Выборы прошли в непризнанных республиках Донбасса 11 ноября. На них удержали лидерство действующие руководители и партии — Денис Пушилин и «Донецкая республика» в ДНР и Леонид Пасечник с движением «Мир Луганщине» в ЛНР. Президент Франции Эмманюэль Макрон и канцлер ФРГ Ангела Меркель после встречи с украинским лидером Петром Порошенко осудили проведение выборов, заявив, что они нелегитимны и «подрывают территориальную целостность и суверенитет Украины». Позже к осуждению присоединились США с обещаниями новых санкций для России.'

>>> navec = Navec.load('navec_news_v1_1B_250K_300d_100q.tar')

>>> tagger = NERTagger.load('slovnet_ner_v1.tar', navec)
>>> markup = tagger(text)
>>> markup
SpanMarkup(
    text='Европейский союз добавил в санкционный список девять политических деятелей из самопровозглашенных республик Донбасса — Донецкой народной республики (ДНР) и Луганской народной республики (ЛНР) — в связи с прошедшими там выборами. Об этом говорится в документе, опубликованном в официальном журнале Евросоюза. В новом списке фигурирует Леонид Пасечник, который по итогам выборов стал главой ЛНР. Помимо него там присутствуют Владимир Бидевка и Денис Мирошниченко, председатели законодательных органов ДНР и ЛНР, а также Ольга Позднякова и Елена Кравченко, председатели ЦИК обеих республик. Выборы прошли в непризнанных республиках Донбасса 11 ноября. На них удержали лидерство действующие руководители и партии — Денис Пушилин и «Донецкая республика» в ДНР и Леонид Пасечник с движением «Мир Луганщине» в ЛНР. Президент Франции Эмманюэль Макрон и канцлер ФРГ Ангела Меркель после встречи с украинским лидером Петром Порошенко осудили проведение выборов, заявив, что они нелегитимны и «подрывают территориальную целостность и суверенитет Украины». Позже к осуждению присоединились США с обещаниями новых санкций для России.',
    spans=[Span(
         start=0,
         stop=16,
         type='LOC'
     ), Span(
         start=108,
         stop=116,
         type='LOC'
     ), Span(
         start=119,
         stop=153,
         type='LOC'
     )
 ...
])

>>> show_ascii_markup(markup.text, markup.spans)

Европейский союз добавил в санкционный список девять политических 
LOC-------------                                                  
деятелей из самопровозглашенных республик Донбасса — Донецкой народной
                                          LOC-----   LOC--------------
 республики (ДНР) и Луганской народной республики (ЛНР) — в связи с 
-----------------   LOC--------------------------------             
прошедшими там выборами. Об этом говорится в документе, опубликованном
 в официальном журнале Евросоюза. В новом списке фигурирует Леонид 
                       LOC------                            PER----
Пасечник, который по итогам выборов стал главой ЛНР. Помимо него там 
--------                                        LOC                  
присутствуют Владимир Бидевка и Денис Мирошниченко, председатели 
             PER-------------   PER---------------               
законодательных органов ДНР и ЛНР, а также Ольга Позднякова и Елена 
                        LOC   LOC          PER-------------   PER---
Кравченко, председатели ЦИК обеих республик. Выборы прошли в 
---------               ORG                                  
непризнанных республиках Донбасса 11 ноября. На них удержали лидерство
                         LOC-----                                     
 действующие руководители и партии — Денис Пушилин и «Донецкая 
                                     PER----------    ORG------
республика» в ДНР и Леонид Пасечник с движением «Мир Луганщине» в ЛНР.
----------    LOC   PER------------              ORG----------    LOC 
 Президент Франции Эмманюэль Макрон и канцлер ФРГ Ангела Меркель после
           LOC---- PER-------------           LOC PER-----------      
 встречи с украинским лидером Петром Порошенко осудили проведение 
                              PER-------------                    
выборов, заявив, что они нелегитимны и «подрывают территориальную 
целостность и суверенитет Украины». Позже к осуждению присоединились 
                          LOC----                                    
США с обещаниями новых санкций для России.
LOC                                LOC--- 

```

## Downloads

<a href="https://github.com/natasha/slovnet/releases/download/v0.0.0/slovnet_ner_v1.tar">slovnet_ner_v1.tar</a> 1.5 MB

## Evaluation

4 datasets are used for evaluation, see <a href="https://github.com/natasha/corus">Corus</a> registry for more info: <a href="https://github.com/natasha/corus#load_factru"><code>factru</code></a>, <a href="https://github.com/natasha/corus#load_gareev"><code>gareev</code></a>, <a href="https://github.com/natasha/corus#load_ne5"><code>ne5</code></a> and <a href="https://github.com/natasha/corus#load_bsnlp"><code>bsnlp</code></a>.

`slovnet` is compared to:

* `deeppavlov` — biLSTM + CRF by DeepPavlov, see <a href="https://arxiv.org/pdf/1709.09686.pdf">their 2017 paper</a> for more.
* `deeppavlov_bert` — BERT based NER, current SOTA for Russian language, see <a href="https://www.youtube.com/watch?v=eKTA8i8s-zs">video presentation</a> describing the approach.
* <a href="http://pullenti.ru/">`pullenti`</a> — first place on factRuEval-2016, super sophisticated ruled based system.
* <a href="https://texterra.ispras.ru">`texterra`</a> — multifunctional NLP solution by <a href="https://www.ispras.ru/">ISP RAS</a>, NER is one of the features.
* <a href="https://github.com/yandex/tomita-parser/">`tomita`</a> — GLR-parser by Yandex, only grammars for `PER` are publicly available.
* <a href="https://github.com/mit-nlp/MITIE">`mitie`</a> — engine developed at MIT + <a href="http://lang.org.ua/en/models/">third party model for Russian language</a>.

For every column top 3 results are highlighted. In each case `slovnet` and `deeppavlov_bert` are 5-10% better then other systems:

<table border="0" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="3" halign="left">factru</th>
      <th colspan="2" halign="left">gareev</th>
      <th colspan="3" halign="left">ne5</th>
      <th colspan="3" halign="left">bsnlp</th>
    </tr>
    <tr>
      <th>f1</th>
      <th>PER</th>
      <th>LOC</th>
      <th>ORG</th>
      <th>PER</th>
      <th>ORG</th>
      <th>PER</th>
      <th>LOC</th>
      <th>ORG</th>
      <th>PER</th>
      <th>LOC</th>
      <th>ORG</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>slovnet</th>
      <td><b>0.960</b></td>
      <td><b>0.905</b></td>
      <td><b>0.808</b></td>
      <td><b>0.982</b></td>
      <td><b>0.907</b></td>
      <td><b>0.992</b></td>
      <td><b>0.981</b></td>
      <td><b>0.958</b></td>
      <td><b>0.949</b></td>
      <td><b>0.833</b></td>
      <td><b>0.735</b></td>
    </tr>
    <tr>
      <th>deeppavlov_bert</th>
      <td><b>0.971</b></td>
      <td><b>0.925</b></td>
      <td><b>0.823</b></td>
      <td><b>0.984</b></td>
      <td><b>0.917</b></td>
      <td><b>0.997</b></td>
      <td><b>0.991</b></td>
      <td><b>0.977</b></td>
      <td><b>0.955</b></td>
      <td><b>0.840</b></td>
      <td><b>0.738</b></td>
    </tr>
    <tr>
      <th>deeppavlov</th>
      <td>0.909</td>
      <td><b>0.885</b></td>
      <td><b>0.735</b></td>
      <td><b>0.944</b></td>
      <td><b>0.797</b></td>
      <td>0.942</td>
      <td><b>0.919</b></td>
      <td><b>0.880</b></td>
      <td>0.866</td>
      <td>0.767</td>
      <td><b>0.623</b></td>
    </tr>
    <tr>
      <th>pullenti</th>
      <td>0.903</td>
      <td>0.814</td>
      <td>0.684</td>
      <td>0.941</td>
      <td>0.641</td>
      <td><b>0.947</b></td>
      <td>0.862</td>
      <td>0.681</td>
      <td><b>0.896</b></td>
      <td>0.766</td>
      <td>0.566</td>
    </tr>
    <tr>
      <th>texterra</th>
      <td>0.900</td>
      <td>0.800</td>
      <td>0.601</td>
      <td>0.888</td>
      <td>0.565</td>
      <td>0.901</td>
      <td>0.777</td>
      <td>0.593</td>
      <td>0.858</td>
      <td>0.783</td>
      <td>0.540</td>
    </tr>
    <tr>
      <th>tomita</th>
      <td><b>0.929</b></td>
      <td></td>
      <td></td>
      <td>0.921</td>
      <td></td>
      <td>0.945</td>
      <td></td>
      <td></td>
      <td>0.880</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>mitie</th>
      <td>0.888</td>
      <td>0.859</td>
      <td>0.531</td>
      <td>0.849</td>
      <td>0.452</td>
      <td>0.753</td>
      <td>0.642</td>
      <td>0.430</td>
      <td>0.733</td>
      <td><b>0.800</b></td>
      <td>0.520</td>
    </tr>
  </tbody>
</table>

* `init` — time between system launch and first response. It is convenient for testing and devops to have model that starts quickly. `deeppavlov_bert` and `texterra` take >30 sec to start, `slovnet` takes just ~1 sec.
* `disk` — file size of artefacts one needs to download before using the system: model weights, embeddings, binaries, vocabs. It is inconvenient to deploy large models in production. `deeppavlov` models require >1 GB download, `slovnet` is just 30 MB including embeddings.
* `ram` — average memory consumption. `deeppavlov` systems and `texterra` are memory heavy, `slovnet` consumes ~200 MB of RAM.
* `speed` — number of news articles processed per second, one article is ~1 KB of text. `deeppavlov` systems process texts in batches on GPU, but they are still slover than `tomita`, `mitie` and `slovnet` that run on single CPU.

<table border="0" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>init, s</th>
      <th>disk, mb</th>
      <th>ram, mb</th>
      <th>speed, articles/s</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>slovnet</th>
      <td><b>0.9</b></td>
      <td><b>30</b></td>
      <td><b>180</b></td>
      <td><b>33.9</b></td>
    </tr>
    <tr>
      <th>deeppavlov_bert</th>
      <td>34.5</td>
      <td>2048</td>
      <td>6144</td>
      <td>13.1 (gpu)</td>
    </tr>
    <tr>
      <th>deeppavlov</th>
      <td>5.9</td>
      <td>1024</td>
      <td>3072</td>
      <td>24.3 (gpu)</td>
    </tr>
    <tr>
      <th>pullenti</th>
      <td><b>2.9</b></td>
      <td><b>16</b></td>
      <td><b>253</b></td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>texterra</th>
      <td>47.6</td>
      <td>193</td>
      <td>3379</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>tomita</th>
      <td><b>2.0</b></td>
      <td><b>64</b></td>
      <td><b>63</b></td>
      <td><b>29.8</b></td>
    </tr>
    <tr>
      <th>mitie</th>
      <td>28.3</td>
      <td>327</td>
      <td>261</td>
      <td><b>32.8</b></td>
    </tr>
  </tbody>
</table>

## License

Source code of `slovnet` is distributed under MIT license (allows modification and commercial usage)

## Support

- Chat — https://telegram.me/natural_language_processing
- Issues — https://github.com/natasha/slovnet/issues
