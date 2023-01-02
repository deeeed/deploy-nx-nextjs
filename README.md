# Goals
- Illustrate build / export with next.js and image management

This is a default next.js project that adds an image (jpg or png) to display.
The image will prevent the export regular export process and will need additional optimization.

## Setup

```bash
yarn nx build
docker build -f ./apps/demo/Dockerfile . -t demo
docker images | grep demo
docker run -p 3000:3000 demo

cat <<EOF >buildkitd.toml
[registry."registry.seyan"]
  http = true
  insecure = true
EOF

docker buildx rm mybuilder
docker buildx create --name mybuilder --config ./buildkitd.toml --use
docker buildx inspect --bootstrap
docker buildx build --platform linux/arm64,linux/amd64 --push -t registry.seyan/demo -f ./apps/demo/Dockerfile .

# Make sure it was deployed
curl -X GET http://registry.seyan/v2/_catalog

```
## Create heml chart
- configure the values in `values.yaml`
```bash
cd helm
# then deploy the chart
helm upgrade --install demo-chart demo/ --values demo/values.yaml
# once done, delete the chart
helm delete demo-chart
```
