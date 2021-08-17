# ec2-spot

```
# EC2 Spot Fleet Request
export SPOT_FLEET_REQUEST_ID=$(aws ec2 request-spot-fleet --spot-fleet-request-config file://spot-fleet-request-config.json | jq -r '.SpotFleetRequestId')

echo $SPOT_FLEET_REQUEST_ID

# Instance IP
export INSTANCE_IP=$(aws ec2 describe-instances | jq -r '.Reservations[0].Instances[0].PublicIpAddress')

# Run Docker merlin-tensorflow-training
ssh -i key_pair_name.pem ubuntu@$INSTANCE_IP "docker run --name merlin-tensorflow -d -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY --runtime=nvidia --rm -it -p 8888:8888 -p 8797:8787 -p 8796:8786 --ipc=host nvcr.io/nvidia/merlin/merlin-tensorflow-training:0.5.3"

# Run jupyter-lab
ssh -i key_pair_name.pem ubuntu@$INSTANCE_IP "docker exec -ti merlin-tensorflow cd /nvtabular ; jupyter-lab --allow-root --ip='0.0.0.0'"

# Terminate Instance
aws ec2 cancel-spot-fleet-requests --spot-fleet-request-ids $SPOT_FLEET_REQUEST_ID --terminate-instances
```
