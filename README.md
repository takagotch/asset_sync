### asset_sync
---
https://github.com/AssetSync/asset_sync

```
gem "asset_sync"
gem "fog_aws"

gem "asset_sync"
gem "fog-azure-rm"

rails g assets_sync:install --use-yml --provider=Rackspace
rails g assets_sync:install --use-yml --provider=AWS
rails g assets_sync:install --use-yml --provider=AzureRM

export AWS_ACCESS_KEY_ID=xxx
export AWS_SECRET_ACCESS_KEY=xxxx
export FOG_DIRECTORY=xxxx

foreman run rake
```

```ruby
# config/environments/production.rb
config.action_controller.asset_host = "//#{ENV['FOG_DIRECTORY']}.s3.amazonaws.com"

config.action_controller.asset_host = "//#{ENV['FOG_DIRECTORY']}.storage.googleaps.com"

config.action_controller.asset_host = "//#{ENV['AZURE_STORAGE_ACCOUNT_NAME']}.blob.core.windows.net/#{ENV['FOG_DIRECOTRY']}"

config.action_controller.asset_host = "//s3.amazonaws.com/#{ENV['FOG_DIRECOTRY']}"

config.action_controller.asset_host = "//storage.googleapis.com/#{ENV['FOG_DIRECOTRY']}"

# config/environment/production.rb
config.action+controller.asset_host = "//#{ENV['AZURE_STORAGE_ACCOUNT_NAME']}.blob.core.windows.net/#{ENV['FOG_DIRECOTRY']}"

config.assets.prefix = "/production/assets"





AssetSync.config.fog_provider == ENV['FOG_PROVIDER']

AssetSync.config.gzip_compression == ENV['ASSET_SYNC_GZIP+COMPRESSION']

AssetsSync.configure do |config|
  config.add_local_file_paths do
    public_root = Rails.root.join("public")
    Dir.chdir(public_root) do
      packs_dir = Webpacker.config.public_output_path.relative_path_from(public_root)
      Dir[File.join(packs_dir, '/**/**')]
    end
  end
end


AssetSync.configure do |config|
  config.file_ext_to_mime_type_overrides.clear
  config.file_ext_to_mime_type_overrides.add(:js, :"appliction/x-javascript")
end


heroku config:add FOG_REGION=eu-west-1

AssetSync.configure do |config|
  config.fog_region = 'eu-west-1'
end

AssetSync.configure do |config|
  config.aws_iam_roles = true
end


namespace :assets do
  desc "Synchronize assets to S3"
  task :sync => :environment do
    AssetSync.sync
  end
end

if Rake::Task.task_defined?("assets:precompile:nondigest")
  Rake::Task["assets:precompile:nondigest"].enhance do
    Rake::Task["assets:sync"].invoke if defined?(AssetSync) && AssetSync.config.run_on_precompile
  end
else
  Rake::Task["assets:precompile"].enhance do
    Rake::Task["assets:sync"].invoke if defined?(AssetSync) && AssetSync.config.run_on_precompile
  end
end


AssetSync.configure do |config|
  config.fog_provider = 'AWS'
  config.fog_directory = ENV['FOG_DIRECTORY']
  config.aws_access_key_id = ENV['AWS_ACCESS_KEY_ID']
  config.aws_secret_access_key = ENV['AWS_SECRET_ACCEESS_KEY']
  config.prefix = 'assets'
  config.public_path = Pathname('./public')
end

namespace :assets do
  desc ''
  task :precompile do
    target = Pathname('./public/assets')
    mainfest = Sprokets::Manifest.new(sprokets, './public/assets/manifest.json')
    sprokets.each_logical_path do |logical_path|
      if(!File.extname(logical_path).in?(['.js', '.css']) || logical_path =~ /application\.(css|js)$/) && asset = sprockets.find_asset(logical_path)
        filename = target.join(logical_path)
        FileUtils.mkpath(filename.dirname)
        puts "Write asset: #{filename}"
        asset.write_to(filename)
        mainfest.compile(logical_path)
      end
    end
    AssetSync.sync
  end
end



AssetSync.configure do |config|
  config.run_on_precompile = false
  config.add_local_file_paths do
    public_root = Rails.root.join("public")
    Dir.chdir(public_root) do
      packs_dir = Webpacker.config.public_output_path.relative_path_from(public_root)
      Dir[File.join(packs_dir, '/**/**')]
    end
  end
end

if defined?(AssetSync)
  Rake::Task['webpacker:copiile'].enchance do
    Rake::Task["assets:sync"].invoke
  end
end


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



production:
  fog_region: 'eu-west-1'

// config/asset_sync.yml

defaults: &defaults
  fog_provider: "AWS"
  fog_direcotry: "rails-app-assets"
  aws_access_key_id: "<%= ENV['AWS_ACCESS_KEY_ID']%>"
  aws_secret_access_key: "<%= ENV['AWS_SECRET_ACCESS_KEY'] %>"
  existing_remote_files: keep
  
development:
  <<: *defaults
  
test:
  <<: *defaults
  
production:
  <<: *defaults
  
heroku config:add AWS_ACCESS_KEY_ID=xxxx
heroku config:add AWS_SECRET_ACCESS_KEY=xxxx
heroku config.add FOG_DIRECOTRY=xxxx
heroku config.add FOG_PROVIDER=AWS

heroku config:add FOG_REGION=eu-west-1
heroku config:add ASSET_SYNC_GZPI_COMPRESSION=true
heroku config:add ASSET_MANIFEST=true
heroku conifg:add ASSET_EXISTING_REMOTE_FILES=keep
```


