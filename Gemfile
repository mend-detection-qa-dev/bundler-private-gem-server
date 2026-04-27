source "https://rubygems.org"

ruby ">= 3.2"

# Gems resolved from the default rubygems.org source
gem "rack", "~> 3.0"
gem "sinatra", "~> 4.0"

# Gems declared as scoped to a private/secondary gem server.
# In a real setup these would resolve from the private registry;
# for this manifest-only probe the block exercises Mend's parser
# when it encounters a non-rubygems.org source declaration.
source "https://gems.example.com" do
  gem "acme-internal-auth", "~> 1.0"
end
