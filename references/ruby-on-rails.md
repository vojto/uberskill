# Ruby on Rails
## 🧪 Testing
- Use RSpec for all Ruby on Rails tests. Do not use Minitest.
- Do not write specs that assert whether logging happened or inspect log output. Logs are diagnostic, not application behavior.
***
## 🔐 Deploying Secrets
When adding a production environment variable, wire it through both the deployment config and CI workflow. Add the CI repository secret, redeploy, and verify the variable is present inside the running container.
