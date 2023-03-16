


### list repositories

curl -k -s -X GET https://localhost:5000/v2/_catalog


REPO_URL=localhost:5000


curl -k -s -X GET https://$REPO_URL/v2/_catalog \
 | jq '.repositories[]' \
 | sort \
 | xargs -I _ curl -s -k -X GET https://$REPO_URL/v2/_/tags/list \
 | jq '.tags[]'




### Get specific image

curl -k -s -X GET https://$REPO_URL/v2/rerenew/tags/list | jq '.tags[]'






### Delete old images

https://stackoverflow.com/questions/25436742/how-to-delete-images-from-a-private-docker-registry


# Clean images with tag latest

docker exec -it cd050b37bdd1 sh

rm -rf /var/lib/registry/docker/registry/v2/repositories/$IMAGE_NAME

cd /

bin/registry garbage-collect  /etc/docker/registry/config.yml