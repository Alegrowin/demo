# Regression with MindsDB

## Prerequisite for testing locally

1. Start MindsDB with [Docker](https://docs.mindsdb.com/setup/self-hosted/docker)

## Sentiment Analysis with HugginFace models

```sql
-- Create model for english sentiment
-- https://huggingface.co/cardiffnlp/twitter-xlm-roberta-base-sentiment
CREATE MODEL sentiment_classifier
PREDICT sentiment
USING
engine='huggingface',
model_name= 'cardiffnlp/twitter-roberta-base-sentiment',
task='text-classification',
input_column = 'comment',
labels=['negative','neutral','positive'];

-- Create model for multi-language
-- https://huggingface.co/cardiffnlp/twitter-xlm-roberta-base-sentiment
CREATE MODEL sentiment_classifier_xlm
PREDICT sentiment
USING
engine='huggingface',
model_name= 'cardiffnlp/twitter-xlm-roberta-base-sentiment-multilingual',
task='text-classification',
input_column = 'comment',
labels=['negative','neutral','positive'];

DESCRIBE MODEL sentiment_classifier_xlm;

SELECT * FROM sentiment_classifier_xlm
WHERE comment="Le AI Dev Dev, c'est oui";

SELECT * FROM sentiment_classifier_xlm
WHERE comment='Le AI Dev Dev, un événement magique';

SELECT * FROM sentiment_classifier_xlm
WHERE comment='Le AI Dev Dev et sa crowd de feu';

SELECT * FROM sentiment_classifier_xlm
WHERE comment='Le AI Dev Dev est à Sherbrooke';

SELECT * FROM sentiment_classifier_xlm
WHERE comment='Cette démonstration tire malheureusement à sa fin`';


DROP MODEL  mindsdb.sentiment_classifier_xlm;
```