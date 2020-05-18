The API server could access it via https://[[HOST_SUBDOMAIN]]-30070-[[KATACODA_HOST]].environments.katacoda.com/api/predict

Below are the sample data that we are passing to the api server and we could successfully parse the attirbutes named Organization from the given data.

```
Google LLC is an American multinational technology company that specializes in Internet-related services and products, which include online advertising technologies, a search engine, cloud computing, software, and hardware. It is considered one of the Big Four technology companies, alongside Amazon, Apple, and Microsoft .[10][11][12]
```

Let's perform a POST request and see how the result would be....

```
curl -X POST \
  https://[[HOST_SUBDOMAIN]]-30070-[[KATACODA_HOST]].environments.katacoda.com/api/predict \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache' \
  -d '{
	"text": "Google LLC is an American multinational technology company that specializes in Internet-related services and products, which include online advertising technologies, a search engine, cloud computing, software, and hardware. It is considered one of the Big Four technology companies, alongside Amazon, Apple, and Microsoft .[10][11][12]"
}'
```{{ execute }}

That's it. Happy Learning !!!

Ref: 

1. http://nlpprogress.com/english/named_entity_recognition.html
2. https://www.weave.works/blog/automating-kubeflow-pipelines-with-gitops-github-actions-and-weave-flagger 
