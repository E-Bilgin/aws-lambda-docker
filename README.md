
Download the model:

```
wget https://github.com/alexeygrigorev/mlbookcamp-code/releases/download/chapter7-model/xception_v4_large_08_0.894.h5
```

Convert the model to tf-lite:

```
python keras_to_tf.py
```

Build the image:

(Important: don't change the workdir in Dockerfile)

```
docker build -t tf-lite-lambda .
```

Run it:

```
docker run --rm \
    -p 8080:8080 \
    tf-lite-lambda
```

Test:

```
URL="http://localhost:8080/2015-03-31/functions/function/invocations"

REQUEST='{
    "url": "http://bit.ly/mlbookcamp-pants"
}'

curl -X POST \
    -H "Content-Type: application/json" \
    --data "${REQUEST}" \
    "${URL}" | jq
```

Response:


```
{
  "dress": -1.8682900667190552,
  "hat": -4.7612457275390625,
  "longsleeve": -2.3169822692871094,
  "outwear": -1.062570571899414,
  "pants": 9.88715648651123,
  "shirt": -2.8124303817749023,
  "shoes": -3.66628360748291,
  "shorts": 3.2003610134124756,
  "skirt": -2.6023387908935547,
  "t-shirt": -4.835044860839844
}
```



Create a repo:

```
aws ecr create-repository --repository-name lambda-images 
```

Tag: 

```
ACCOUNT=XXXXXXXXXXXX

docker tag tf-lite-lambda ${ACCOUNT}.dkr.ecr.eu-west-1.amazonaws.com/lambda-images:tf-lite-lambda
```

Login:

```
$(aws ecr get-login --no-include-email)
```

Push to ECR

```
docker push ${ACCOUNT}.dkr.ecr.eu-west-1.amazonaws.com/lambda-images:tf-lite-lambda
```


Now create a lambda function, give it 1024 RAM and test it