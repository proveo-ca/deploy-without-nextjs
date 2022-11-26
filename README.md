# Deploy without next.js
Bash scripting for a hello world in the cloud; using bundling tools and deployment CLIs.

Nothing against the people at [Vercel](https://vercel.com/), nor [next.js](https://nextjs.org/).

## Why?
It's good to use a tool because it brings value and/or convenience to the workflow...

...But it's better when there is a contrast, and alternatives to compare with.

## Prerequisites
- [Node LTS]([v18](https://nodejs.org/en/)) installed
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) installed
- An [AWS](https://portal.aws.amazon.com/billing/signup) developer account (12 month free trial: requires willingness to give them a credit card)

## The process
3 steps is what we need to deploy. The essence of it, will be an `index.html` file being read from the cloud:

1. Generate the application's static build with [webpack](https://webpack.js.org/) / [parcel](https://github.com/parcel-bundler/parcel) / [vite](https://github.com/vitejs/vite) _(Create the readable `index.html` file. Runnable files, when deploying a server)_
2. Set up a hosting account with [AWS](https://aws.amazon.com/) / [Heroku](https://www.heroku.com/) _(Set up rules in somebody else's computer, aka the cloud)_
3. Deploy through your [CLI](https://techterms.com/definition/command_line_interface). CLI commands are easily runnable everywhere. _(Push your updates to the cloud)_ 