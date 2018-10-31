### asset_sync
---
https://github.com/AssetSync/asset_sync

```
gem "asset_sync"
gem "fog_aws"

gem "asset_sync"
gem "fog-azure-rm"

foreman run rake
```

```ruby
# config/environments/production.rb
config.action_controller.asset_host = "//#{ENV['FOG_DIRECTORY']}.s3.amazonaws.com"

config.action_controller.asset_host = ""

config.action_controller.asset_host = ""


```

```
AWS_ACCESS_KEY_ID=<yourkeyid>
AWS_SECRET_ACCESS_KEY=<yoursecretkey>
FOG_DIRECTORY=<yourbucket>
FOG_REGION=<youbucketregion>

AZURE_STORAGE_ACCOUNT_NAME=<youraccountname>
AZURE_STORAGE_ACCESS_KEY=<youraccesskey>
FOG_DIRECTORY=<yourcontainer>
FOG_REGION=<yourcontainerregion>

```


