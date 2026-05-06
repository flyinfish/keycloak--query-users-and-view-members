# keycloak--query-users-and-view-members

## run
`docker-compose up`

## scenario

there is two groups `A` and `B`. and there are two permissions granting `view-members` on each group based on a specific role.


## verify 

```bash
echo '### logging in'
token=$(curl -s -X POST http://localhost:8080/realms/dev/protocol/openid-connect/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=password&client_id=someclient&username=view-a-1&password=view-a-1" \
  | jq -r '.access_token')

b11direct=$(curl -s -X GET "http://localhost:8080/admin/realms/dev/users/60878e84-73d4-4afa-a135-5e7a0d4e7a1d" \
  -H "Authorization: Bearer $token")
echo
echo "### get b11 expecting 403: $b11direct"

b11=$(curl -s -X GET "http://localhost:8080/admin/realms/dev/users?exact=true&username=b11" \
  -H "Authorization: Bearer $token")
echo
echo "### query for b11 expecting no result: $b11"

a11direct=$(curl -s -X GET "http://localhost:8080/admin/realms/dev/users/74aab3d7-d4c0-4ca4-910e-96f04550a8d0" \
  -H "Authorization: Bearer $token")
echo
echo "### get a11 expecting 200: $a11direct"

a11=$(curl -s -X GET "http://localhost:8080/admin/realms/dev/users?exact=true&username=a11" \
  -H "Authorization: Bearer $token")
echo
echo "### query for a11 expecting a result: $a11"

aa11direct=$(curl -s -X GET "http://localhost:8080/admin/realms/dev/users/33e969f8-25d8-4269-9376-34c980dbf2a7" \
  -H "Authorization: Bearer $token")
echo
echo "### get aa11 expecting 200: $aa11direct"

aa11=$(curl -s -X GET "http://localhost:8080/admin/realms/dev/users?exact=true&username=aa11" \
  -H "Authorization: Bearer $token")
echo
echo "### query for aa11 expecting a result BUT GET EMPTY: $aa11"
```

## modify testcontainer and export it

```
container="..."

docker exec -it $container sh -c \
  "cp -rp /opt/keycloak/data/h2 /tmp ; \
  export KC_DEBUG=false ; \
  /opt/keycloak/bin/kc.sh export --realm dev --file /tmp/dev-realm.json \
    --http-management-port 9001 \
    --db dev-file \
    --db-url 'jdbc:h2:file:/tmp/h2/keycloakdb;NON_KEYWORDS=VALUE'"\
&& docker cp $container:/tmp/dev-realm.json  import/dev-realm.json
```
# keycloak--query-users-and-view-members
