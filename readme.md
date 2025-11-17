# Hello World Next.js starter for Uniform

This starter contains the essentials with pre-enabled Uniform capabilities to run in [NextJS pages router SSG mode](https://nextjs.org/docs/pages/building-your-application/rendering/static-site-generation) to generate and upload the static site into the Azure blob storage

## Getting started

1. `npm install`
1. Setup your own project ID and API key values in `.env` with the API key that has Developer role.
1. `npm run uniform:push` to push content.
1. `npm run dev` and open http://localhost:3000

## Rendering modes

Both SSR (default) and SSG are supported, see `/pages/[[...slug]].tsx.ssg` and `/pages/[[...slug]].tsx.ssr` and enable the right mode for your use case. The app is currently configured in SSG mode (`output: "export"` in the `next.config.js` and `withUniformGetStaticProps` in `pages/[[...slug]].tsx`) to make it deploy to the Azure blob storage as just static files.

## Azure pipelines setup

The `azure-pipelines.yml` file defines a CI/CD pipeline for building and deploying the application to Azure. It automatically triggers on pushes to the `main` branch or publishing a composition in Uniform, please check the next section for more details.

Pipeline steps:

- Install Node.js
- Install dependencies
- Build the project (generate the static site)
- Upload the generated `out` folder with the static site to Azure Blob Storage under the `$web` container,
- Purge the Azure Front Door CDN cache to ensure changes are visible immediately.

> Purging the cache doesn't have to be done from inside the pipeline, it can also be added via Uniform webhook as another call.

To run this pipeline successfully, the following environment variables must be set in Azure DevOps:

- `UNIFORM_API_KEY`: The API key for accessing the Uniform project.
- `UNIFORM_PROJECT_ID`: The Uniform project ID.
- `STORAGE_KEY`: The access key for the Azure Storage account specified in `STORAGE_ACCOUNT_NAME`.

> You can configure any Azure DevOps supported upload technique instead of STORAGE_KEY if needed. The goal is to upload the content inside the `out` folder.

Make sure these variables are provided through your Azure DevOps pipeline environment or variable groups for secure and effective deployment.

## Triggering the Azure pipeline on Uniform composition publish

You can use [Uniform webhooks](https://docs.uniform.app/docs/guides/webhooks) to trigger the Azure DevOps pipeline via an HTTP request. For example, subscribe to the Uniform composition.published event and add endpoint like this:

`https://dev.azure.com/<organization>/<project>/_apis/pipelines/<pipeline_number>/runs?api-version=7.1`

More details:
https://learn.microsoft.com/en-us/rest/api/azure/devops/pipelines/runs/run-pipeline?view=azure-devops-rest-7.1
