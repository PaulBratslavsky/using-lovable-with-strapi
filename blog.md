# How To Build a Frontend with Lovable.dev for Your Strapi Backend

AI tools like [Lovable.dev](https://lovable.dev) and [Claude Code](https://www.anthropic.com/claude-code) can help you spin up projects faster than ever. But from my experience in vibe coding, I've learned they're not magic bullets—you still need to know what you're building, understand your data, the problems you're trying to solve, and the tech stack you're using to get good output. (And yes—you still need to know how to code.)

These tools are powerful, but they're also prone to hallucinations and mistakes. The better you understand your project's architecture and how these tools work, the more effective you'll be.

In this post, I'll share a high-level overview of building a frontend with Lovable.dev connected to a Strapi backend. There's a lot to unpack, so I'll follow up with deeper dives and answer questions in future posts.

## What is Lovable.dev?

Lovable.dev is a browser-based AI-powered development platform that generates full-stack web applications using natural language prompts. Similar to V0 by Vercel, Lovable takes your descriptions and turns them into working code—but goes further by providing a complete development environment with instant preview, version control, and deployment capabilities.

**Key features of Lovable.dev include:**

- **AI-assisted full-stack development**: Describe what you want in natural language, and Lovable generates React components, pages, backend logic, and database schemas.
- **Instant preview and iteration**: See your changes live as Lovable builds your application. Refine and adjust with follow-up prompts.
- **Integrated deployment**: Deploy your application directly from Lovable to production with a few clicks.
- **Real-time collaboration**: Work alongside the AI, reviewing and guiding the generated code as it builds your application.

## What is Vibe Coding?

Vibe coding is a chill, creative development session where you use AI tools to prototype and build applications in a flow state.

Instead of the traditional, step-by-step coding from scratch, you describe what you want in natural language and let the AI generate the code, guiding it iteratively.

![lovable-gif.gib](./images/lovable-gif.gif)

The "vibe" comes from maintaining momentum and curiosity—trying things out quickly without getting bogged down in setup or syntax issues.

**Key characteristics of vibe coding include:**

- **AI-assisted coding**: You use an AI tool (like Lovable.dev or V0) as a pair programmer. The AI can generate React components, pages, styles, and even connect to APIs based on your instructions.
- **Rapid prototyping**: The goal is to get a working prototype up and running quickly. It's about turning ideas into working code in minutes or hours, not days, by letting the AI handle the heavy lifting of boilerplate code generation.
- **Iterative exploration**: You don't necessarily have a fully specified end product from the start. Instead, you prompt the AI to build one piece at a time, refining and adding features in short cycles.
- **Human oversight**: Importantly, vibe coding doesn't mean the AI does everything perfectly. Think of it like working with a junior developer—you get raw productivity, but you also need to review and guide carefully.

**What is Claude Code?** (by Anthropic) A terminal-based AI coding assistant that acts as an autonomous developer—able to read, write, debug, and execute code across your full-stack project, guided by you.

## Why Use Lovable.dev with Strapi?

When building a content-driven application, you have several approaches:

1. **Start with the frontend** - Design and build the UI first, then create the backend to match
2. **Start with the backend** - Model your data and API first, then build the frontend to consume it
3. **Build them in parallel** - Develop both simultaneously (often leads to mismatches and rework)

**For this tutorial, we're taking the backend-first approach with Strapi. Here's why:**

### Pros of Starting with Strapi Backend:

- **Data structure defines your UI**: Your content model naturally informs what components and pages you need
- **Type safety from the start**: Generate TypeScript types from your API schema, reducing runtime errors
- **Real data during development**: Test with actual content instead of mock data
- **OpenAPI specification**: Strapi can generate an OpenAPI spec that AI tools can consume, providing perfect context about your API structure
- **Content flexibility**: Non-technical users can start adding content while you build the frontend

### Cons of Backend-First Approach:

- **Upfront planning required**: You need to think through your content model before seeing visual results
- **Potential for over-engineering**: Easy to add fields and relationships you might not need
- **Less visual feedback early**: You won't see the UI until later in the process

Despite the cons, the backend-first approach with Strapi provides the strongest foundation for AI-assisted frontend development—especially when using OpenAPI specifications.

## What is OpenAPI and Why Should You Use It?

**OpenAPI Specification (formerly Swagger)** is a standardized, language-agnostic way to describe RESTful APIs. It's essentially a contract that defines:

- Available endpoints and operations (GET, POST, PUT, DELETE, etc.)
- Request parameters and their types
- Request and response body schemas
- Authentication methods
- Error responses

Here's a simple example of what an OpenAPI spec looks like:

```yaml
openapi: 3.0.0
paths:
  /api/articles:
    get:
      summary: Get all articles
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: "#/components/schemas/Article"
components:
  schemas:
    Article:
      type: object
      properties:
        id:
          type: integer
        title:
          type: string
        content:
          type: string
```

### Why OpenAPI is Perfect for AI-Assisted Development:

1. **Eliminates hallucinations**: Instead of the AI guessing your API structure, it has the exact specification
2. **Type-safe code generation**: AI can generate TypeScript interfaces that perfectly match your backend
3. **Accurate API calls**: The AI knows exactly what endpoints exist, what parameters they accept, and what they return
4. **Documentation as context**: OpenAPI serves as both human and machine-readable documentation
5. **Automatic validation**: You can validate requests/responses against the spec

### Generating OpenAPI Spec in Strapi

Strapi makes it incredibly easy to generate an OpenAPI specification for your API:

```bash
# Generate OpenAPI spec
npx strapi openapi generate

# This creates an openapi.json file in your project root
```

![swaggerapi.gif](./images/swaggerapi.gif)

The generated spec includes all your content types, components, and API endpoints with complete type definitions—perfect for feeding into AI tools like Lovable.dev.

I am using **OpenAPI** extension in VS Code to preview the endpoints.

![swagger-extension.png](./images/swagger-extension.png)

We will go over in more detail how we can use this later in the tutorial.

## Let's Start: Building a Frontend with Lovable.dev for Your Strapi Backend

Now let's walk through the actual process of building a frontend with Lovable.dev connected to a Strapi backend.

### Prerequisites

Before getting started, here are the key questions to ask:

- ✅ Do you already have a Strapi project? (If not, we'll create one)
- ✅ Do you have content types defined in Strapi?
- ✅ Is your Strapi API accessible (locally or deployed)?

**Important Safety Note:** Never point your AI tools directly at your production database or server. Always use a dev environment. It's surprisingly easy to trigger thousands of API requests in seconds and accidentally DDoS yourself—or cause even bigger problems—if you're not careful.

### Step 1: Set Up Your Strapi Project

If you don't have a Strapi project, no worries! We'll use one I built for my Strapi Crash Course. You can check out the video tutorial if you'd like to understand how the backend was created.

// TODO: embed video here later

However, since we're focusing on building the frontend today, you can simply clone the backend project from the following repo.

```bash
git clone https://github.com/PaulBratslavsky/pauls-strapi-crashcourse server
```

Now that we have our project, let's change directories into the `server` folder:

```bash
cd server
```

Next, install all the dependencies. You can use either `yarn` or `npm`:

```bash
yarn
```

or

```bash
npm install
```

Once the dependencies are installed, create a `.env` file in the root of the project. You can copy the configuration from the `.env.example` file that's already included:

```bash
HOST=0.0.0.0
PORT=1337
APP_KEYS="toBeModified1,toBeModified2"
API_TOKEN_SALT=tobemodified
ADMIN_JWT_SECRET=tobemodified
TRANSFER_TOKEN_SALT=tobemodified
JWT_SECRET=tobemodified
ENCRYPTION_KEY=tobemodified
```

Replace each placeholder string with a unique value of your choice.

Now seed the project with initial data by running:

```bash
yarn strapi import -f ./seed-data.tar.gz
```

When prompted to confirm, type `y` for Yes:

```bash
➜  server git:(main) yarn strapi import -f ./seed-data.tar.gz
yarn run v1.22.22
$ strapi import -f ./seed-data.tar.gz
? The import will delete your existing data! Are you sure you want to proceed? (y/N) y
```

And type `y` for the following:

```bash
[2025-10-24 10:09:33.381] warn: (Schema Integrity) admin::session does not exist on source
? There are differences in schema between the source and destination, and the data listed above will be lost. Are you sure you want
to continue? (y/N)
```

This is just a warning about a schema mismatch between what's generated locally versus what's in the seed file. It's safe to ignore.

Once the import finishes, you should see the following output:

```bash
Starting import...
✔ entities: 55 transferred (size: 46.7 KB) (elapsed: 64 ms) (729.6 KB/s)
✔ assets: 8 transferred (size: 300 KB) (elapsed: 21 ms) (13.9 MB/s)
✔ links: 171 transferred (size: 32.2 KB) (elapsed: 11 ms) (2.9 MB/s)
✔ configuration: 47 transferred (size: 135.4 KB) (elapsed: 8 ms) (16.5 MB/s)
┌─────────────────────────────────────────┬───────┬───────────────┐
│ Type                                    │ Count │ Size          │
├─────────────────────────────────────────┼───────┼───────────────┤
│ entities                                │    55 │      46.7 KB  │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- api::article.article                 │    12 │ (    22.3 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- api::author.author                   │     2 │ (     534 B ) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- api::global.global                   │     2 │ (     3.3 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- api::landing-page.landing-page       │     2 │ (     7.9 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- api::page.page                       │     4 │ (     1.6 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- api::tag.tag                         │     3 │ (     820 B ) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- plugin::i18n.locale                  │     1 │ (     253 B ) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- plugin::upload.file                  │     8 │ (     4.1 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- plugin::upload.folder                │     2 │ (     519 B ) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- plugin::users-permissions.permission │    16 │ (     4.4 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- plugin::users-permissions.role       │     2 │ (     656 B ) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- plugin::users-permissions.user       │     1 │ (     464 B ) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ assets                                  │     8 │       300 KB  │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- .avif                                │     3 │ (   292.1 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- .svg                                 │     5 │ (     7.9 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ links                                   │   171 │      32.2 KB  │
├─────────────────────────────────────────┼───────┼───────────────┤
│ configuration                           │    47 │     135.4 KB  │
├─────────────────────────────────────────┼───────┼───────────────┤
│ Total                                   │   281 │     514.2 KB  │
└─────────────────────────────────────────┴───────┴───────────────┘
Import process has been completed successfully!
✨  Done in 15.24s.
```

We are not ready to start our Strapi project. We can do this by running the following command in out `server` directory:

```bash
yarn dev
```

Once our project starts, you can navigate to it at [http://localhost:1337](http://localhost:1337)

You will be greeted with the following screen. Let's go ahead and create our first **Admin** user.

![001-strapi-admin-login.png](./images/001-strapi-admin-login.png)

Once you create your user, you will be greeted by the Strapi dashboard.

![002-strapi-admin-dashboard.png](./images/002-strapi-admin-dashboard.png)

Nice, now that we have our project running, let's do a quick overview of our content types.

Below is an example of the articles content type and how to access its data via the API.

![articles-content-and-endpoint.gif](./images/articles-content-and-endpoint.gif)

Our Strapi backend includes several content types located in `/server/src/api`. For this tutorial, we'll focus on building three key pages in Lovable:

1. **Landing Page** - Uses dynamic zones for flexible content composition
2. **Articles Listing Page** - Displays all published articles
3. **Article Detail Page** - Shows individual article content

Here's an overview of the main content types we'll be working with:

**Global Content Type** (`api::global.global`):

- title (Text)
- description (Text)
- banner (Component - layout.banner)
- header (Component - layout.header)
- footer (Component - layout.footer)

**Article Content Type** (`api::article.article`):

- title (Text)
- description (Text)
- slug (UID based on title)
- content (Rich Text)
- featuredImage (Media - Single)
- author (Relation to Author - manyToOne)
- contentTags (Relation to Tag - oneToMany)
- blocks (Dynamic Zone - for flexible content sections)
- relatedArticles (Relation to Article - oneToMany)

**Landing Page Content Type** (`api::landing-page.landing-page`):

- title (Text)
- description (Text)
- blocks (Dynamic Zone with components):
  - Hero
  - Section Heading
  - Card Grid
  - Content with Image
  - Markdown
  - Person Card
  - FAQs
  - Newsletter
  - Featured Articles

**Author Content Type** (`api::author.author`):

- name (Text)
- bio (Text)
- avatar (Media - Single)
- articles (Relation to Article - oneToMany)

**Tag Content Type** (`api::tag.tag`):

- name (Text)
- slug (UID based on name)

### Enabling Public API Access

The seed data already includes public access permissions for these content types (Settings → Users & Permissions → Roles → Public). However, if you're setting up your own Strapi project from scratch, you'll need to enable public access for each content type's `find` and `findOne` operations. This makes the API endpoints accessible without authentication.

To verify or enable public access:

1. Navigate to **Settings → Users & Permissions Plugin → Roles → Public**
2. Expand each content type (Article, Landing Page, Author, Tag)
3. Check the boxes for `find` and `findOne` permissions
4. Click **Save**

![users-permission.png](./images/users-permission.png)

### API Endpoints We'll Use

For this tutorial, we'll be working with four main API endpoints:

1. **Global Settings** (Single Type):
   **GET**
   [`http://localhost:1337/api/global`](http://localhost:1337/api/global)

   This endpoint returns the global website settings including header navigation, footer content, and banner information. This data will be used across all pages of your frontend.

   ![global.png](./images/global.png)

2. **Landing Page** (Single Type):
   **GET**
   [`http://localhost:1337/api/landing-page`](http://localhost:1337/api/landing-page)

   This endpoint returns the landing page with all its dynamic zone components populated.

   ![landing-page.png](./images/landing-page.png)

3. **Articles List** (Collection Type):
   **GET**
   [`http://localhost:1337/api/articles`](http://localhost:1337/api/articles)

   This endpoint returns all published articles with their relations (author, tags, featured image).

   ![article.png](./images/article.png)

4. **Single Article by Slug** (Collection Type):
   **GET**
   [`http://localhost:1337/api/articles?filters[slug][$eq]=why-java-script-is-still-the-most-popular-programming-language`](http://localhost:1337/api/articles?filters[slug][$eq]=why-java-script-is-still-the-most-popular-programming-language)

   This endpoint fetches a specific article using Strapi's filter syntax based on the slug field.

**Notes:**

- The `filters[slug][$eq]` syntax is Strapi's way of filtering by field values
- All URLs use port `1337` (Strapi's default port)

You can test these endpoints directly in your browser or use a tool like Postman to see the response structure before building the frontend.

### Step 2: Generate Your OpenAPI Specification

With your content types created, generate the OpenAPI spec:

We saw the steps earlier, but now let's do it in our local project.

```bash
# In your Strapi project directory
yarn strapi openapi generate
```

This creates a `specification.json` file. You can install the [OpenAPI](https://marketplace.visualstudio.com/items?itemName=42Crunch.vscode-openapi) VS Code extension to visually explore your API documentation.

![swaggerapi.gif](./images/swaggerapi.gif)

### Step 3: Deploy Strapi (Optional but Recommended)

For easier integration with Lovable.dev, deploy your Strapi project to [Strapi Cloud](https://strapi.io/cloud) (free tier available).

Before deploying to Strapi Cloud, let's save the project to your own GitHub repository:

**Step 1: Remove the existing git reference**

```bash
rm -rf .git
```

**Step 2: Initialize a new git repository**

```bash
git init
git add .
git commit -m "Initial commit"
```

**Step 3: Create a new repository on GitHub**

Go to [github.com](https://github.com) and create a new repository (don't initialize it with README, .gitignore, or license).

![create-new-project-repo-web-gif.gif](./images/create-new-project-repo-web-gif.gif)

**Step 4: Push your code to GitHub**

```bash
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
git branch -M main
git push -u origin main
```

Now that we have our project in out own GitHub Repo, let's deploy it to Strapi Cloud.

**Step 5: Deploy to Strapi Cloud**

You have two options for making your Strapi API accessible to Lovable.dev:

**Option A: Use ngrok for local development (Quick & Temporary)**

If you prefer to keep Strapi running locally, you can use ngrok to temporarily expose your local server:

```bash
# Install ngrok first from https://ngrok.com/download
# Then run this command to expose your local Strapi instance
ngrok http 1337
```

This will give you a public URL (like `https://abc123.ngrok.io`) that you can use in Lovable.dev to connect to your local Strapi backend.

**Option B: Deploy to Strapi Cloud (Recommended)**

For a more permanent solution, here's how to deploy your project for free to Strapi Cloud:

Navigate to [Strapi Cloud](https://strapi.io/cloud) and create a new account if you don't already have one.

![strapi-cloud.png](./images/strapi-cloud.png)

Once in your dashboard click on the create project button:

![create-cloud-project.png](./images/create-cloud-project.png)

Choose the Free Plan:

![choose-free-plan.png](./images/choose-free-plan.png)

Now select your account and project:

![select-repo-and-project.png](./images/select-repo-and-project.png)

Now, let's name our Cloud project and select our region.

I will call mine `lovable-example` and pick `US East` region.

![name-your-project-and-select-region.png](./images/name-your-project-and-select-region.png)

Finally click on **Create Project** button to deploy your project:

![deploy-no-content.gif](./images/deploy-no-content.gif)

Once the deployment finishes, you'll be able to navigate to your Strapi instance and create your first admin user. Notice that we don't have any data yet—we can add data by using the transfer command.

First let's create Transfer Token in Strapi.

Navigate to Setting => Transfer Token and click on `Add new transfer token` button.

![create-transfer-token.png](./images/create-transfer-token.png)

Now let's create our token:

- Name: initial transfer
- Token duration: 7 days
- Token type: full access

![token-details.png](./images/token-details.png)

![token-details.png](./images/token-information.png)

Now that we have our token and URL, we can use the CLI to transfer our data to Strapi Cloud.

Run the following command in your local `server` directory:

```bash
yarn strapi transfer
```

When prompted, enter your project URL (in my case it was `https://inviting-cheese-ce66d3b9e0.strapiapp.com/admin`). Make sure to include the `/admin` path.

Then you'll be prompted to enter your transfer token, and finally type `y` to confirm the transfer.

```bash
➜  server git:(main) yarn strapi transfer
yarn run v1.22.22
$ strapi transfer
ℹ️  Data transfer documentation: https://docs.strapi.io/dev-docs/data-management/transfer
ℹ️  No transfer configuration found in environment variables
   → Add STRAPI_TRANSFER_URL and STRAPI_TRANSFER_TOKEN environment variables to make the transfer process faster for future runs
? Choose transfer direction: Push local data to remote Strapi
? Enter the URL of the remote Strapi instance to send data to:
https://inviting-cheese-ce66d3b9e0.strapiapp.com/admin
? Enter the transfer token for the remote Strapi destination:
[hidden]
? The transfer will delete existing data from the remote Strapi! Are
 you sure you want to proceed? Yes
Starting transfer...
✔ entities: 58 transferred (size: 48.1 KB) (elapsed: 4207 ms) (11.4 KB/s)
✔ assets: 8 transferred (size: 300 KB) (elapsed: 1964 ms) (152.7 KB/s)
✔ links: 174 transferred (size: 32.6 KB) (elapsed: 1987 ms) (16.4 KB/s)
✔ configuration: 48 transferred (size: 141.8 KB) (elapsed: 1318 ms) (107.6 KB/s)
┌─────────────────────────────────────────┬───────┬───────────────┐
│ Type                                    │ Count │ Size          │
├─────────────────────────────────────────┼───────┼───────────────┤
│ entities                                │    58 │      48.1 KB  │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- admin::session                       │     3 │ (     1.4 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- api::article.article                 │    12 │ (    22.3 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- api::author.author                   │     2 │ (     534 B ) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- api::global.global                   │     2 │ (     3.3 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- api::landing-page.landing-page       │     2 │ (     7.8 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- api::page.page                       │     4 │ (     1.6 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- api::tag.tag                         │     3 │ (     820 B ) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- plugin::i18n.locale                  │     1 │ (     253 B ) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- plugin::upload.file                  │     8 │ (     4.1 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- plugin::upload.folder                │     2 │ (     519 B ) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- plugin::users-permissions.permission │    16 │ (     4.4 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- plugin::users-permissions.role       │     2 │ (     656 B ) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- plugin::users-permissions.user       │     1 │ (     464 B ) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ assets                                  │     8 │       300 KB  │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- .avif                                │     3 │ (   292.1 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ -- .svg                                 │     5 │ (     7.9 KB) │
├─────────────────────────────────────────┼───────┼───────────────┤
│ links                                   │   174 │      32.6 KB  │
├─────────────────────────────────────────┼───────┼───────────────┤
│ configuration                           │    48 │     141.8 KB  │
├─────────────────────────────────────────┼───────┼───────────────┤
│ Total                                   │   288 │     522.5 KB  │
└─────────────────────────────────────────┴───────┴───────────────┘
Transfer process has been completed successfully!
✨  Done in 76.51s.
➜  server git:(main)

```

You should now see your data in your Strapi Cloud instance. If you navigate to the `api/articles` endpoint, you should see your API data.

**Example:** [https://inviting-cheese-ce66d3b9e0.strapiapp.com/api/articles](https://inviting-cheese-ce66d3b9e0.strapiapp.com/api/articles)

Now that your API is up and running—regardless of whether you used ngrok or Strapi Cloud—we're ready to work on the frontend.

The reason we took time to properly set up our backend is that we can now build our Lovable app based on actual API responses and data structures, which will allow Lovable to be more precise and generate better code.


### Step 4: Prepare Your Context for Lovable.dev

Before jumping into Lovable.dev, gather the following:

1. **Your OpenAPI specification** (openapi.json file)
2. **A sample API response** from an endpoint like `/api/articles`
3. **Your design preferences** (color scheme, layout style, component library preferences)

### Step 5: Craft Your Prompt for Lovable.dev

**Understanding Prompting Approaches**

We're going to start with a **one-shot approach**, which means giving Lovable all the context in a single prompt and having it build as much as possible in one go.

**Pros of One-Shot Prompting:**
- Quick initial setup—get a working foundation fast
- Ensures all requirements are considered together
- Good for establishing overall architecture and structure
- Provides a complete starting point to iterate from

**Cons of One-Shot Prompting:**
- Often produces generic or half-baked implementations
- May miss nuanced requirements or edge cases
- Harder to catch issues early since everything is generated at once
- Can lead to AI hallucinations when trying to handle too many requirements simultaneously

**Recommended Best Practice: Iterative Prompting**

The most effective approach is to **break your build into small, focused prompts**:
1. Start with basic layout and routing
2. Add data fetching and API integration
3. Implement one feature at a time (search, pagination, filtering, etc.)
4. Test each increment before moving on
5. Refine and optimize based on what you see

**Why iterative works better:**
- You can review and validate each piece of generated code
- Easier to catch issues like excessive API calls or logic errors
- Allows you to provide specific feedback and corrections
- Results in higher quality, more maintainable code

**Our Approach:**

We'll start with a detailed one-shot prompt to establish the foundation, then add additional focused prompts as we go along to refine and enhance specific features. This hybrid approach gives us speed while maintaining quality control.

**Understanding the Strapi Client SDK**

Before we craft our prompt, it's important to understand the Strapi Client SDK. The official [@strapi/client](https://github.com/strapi/client) package provides a type-safe way to interact with Strapi APIs.

**Why use the Strapi Client SDK?**
- **Built-in authentication** - Handle JWT tokens automatically
- **Request/response handling** - Proper error handling and data unwrapping
- **Optimized queries** - Helper methods for filtering, pagination, and population

**Basic setup example:**

```typescript
import { strapiClient } from '@strapi/client';

// Initialize the Strapi client
const client = strapiClient({
  baseURL: 'https://inviting-cheese-ce66d3b9e0.strapiapp.com',
  // No authentication needed for public endpoints
});

// 1. Fetch Global settings (single type)
const globalData = await client.single('global').find();
// Returns: { data: { id, documentId, attributes: { title, description, banner, header, footer } } }

// 2. Fetch Landing Page (single type)
const landingPage = await client.single('landing-page').find();
// Returns: { data: { id, documentId, attributes: { title, description, blocks: [...] } } }

// 3. Fetch all Articles with pagination
const articles = await client.collection('articles').find({
  pagination: { page: 1, pageSize: 6 }
});
// Returns: { data: [...], meta: { pagination: { page, pageSize, pageCount, total } } }

// 4. Search articles by title using filters (case-insensitive)
const searchResults = await client.collection('articles').find({
  filters: {
    title: { $containsi: 'javascript' }
  },
tha
// Common Strapi v5 filter operators:
// $eq: equals
// $ne: not equals
// $containsi: contains (case-insensitive)
// $startsWith: starts with
// $endsWith: ends with

// 5. Fetch single article by slug
const article = await client.collection('articles').find({
  filters: {
    slug: {
      $eq: 'why-java-script-is-still-the-most-popular-programming-language'
    }
  }
});
// Returns: { data: [{ id, documentId, attributes: {...} }], meta: {} }
// Note: Returns array with single item - extract first item: article.data[0]
```


**Important Note About Population:**

In our Strapi backend, we're already populating all content via a route middleware, so you don't need to add `populate` parameters in your frontend API calls. The only additional params you'll need are:
- **filter** - To filter articles by slug or other fields
- **pagination** - To implement pagination

**Example of our route middleware:**

Here's how we configured the articles route to automatically populate all relations:

```typescript
// server/src/api/article/routes/article.ts
import { factories } from '@strapi/strapi';

export default factories.createCoreRouter('api::article.article', {
  config: {
    find: {
      "middlewares": ["api::article.article-populate"]
    }
  }
});
```

**The populate middleware:**

```typescript
// server/src/api/article/middlewares/article-populate.ts
import type { Core } from "@strapi/strapi";

const populate = {
  featuredImage: {
    fields: ["url", "alternativeText"],
  },
  author: {
    populate: {
      image: {
        fields: ["url", "alternativeText"],
      },
      articles: {
        fields: ["documentId", "title"],
      },
    },
  },
  contentTags: true,
  blocks: {
    on: {
      "blocks.hero": {
        populate: { links: true, image: { fields: ["alternativeText", "url"] } },
      },
      "blocks.section-heading": true,
      "blocks.card-grid": { populate: { cards: true } },
      "blocks.content-with-image": {
        populate: { link: true, image: { fields: ["alternativeText", "url"] } },
      },
      "blocks.markdown": true,
      "blocks.person-card": {
        populate: { image: { fields: ["alternativeText", "url"] } },
      },
      "blocks.faqs": { populate: { faq: true } },
      "blocks.newsletter": true,
    },
  },
  relatedArticles: {
    populate: {
      featuredImage: { fields: ["alternativeText", "url"] },
      author: true,
    },
  },
};

export default (config, { strapi }: { strapi: Core.Strapi }) => {
  return async (ctx, next) => {
    ctx.query.populate = populate;
    await next();
  };
};
```

This means when you call `/api/articles`, all relations, images, and dynamic zone blocks are automatically populated—you don't need to add `?populate=deep` or specify individual fields.

While we'll include this SDK in our prompt to Lovable, you could also use a simple fetch wrapper or libraries like Axios. The SDK is recommended for larger projects but not strictly required.

## The Complete Lovable.dev Prompt

Below is the complete prompt you'll paste into Lovable.dev. It includes:
- Your actual Strapi backend URL and data structure
- Complete API utility functions and React Query hooks
- All requirements for pages, components, and features
- Strapi-specific constraints (flat data structure, no populate params, filter syntax)

**Simply copy this entire prompt and paste it into Lovable.dev to generate your full blog frontend:**

```txt
You are a senior frontend developer experienced with TypeScript, React, and building dynamic content-driven websites that consume Strapi RESTful APIs.

TASK: Build a blog platform frontend that consumes a Strapi v5 backend API.

TECH STACK:
- React with TypeScript
- Tailwind CSS for styling
- shadcn/ui for UI components (optional, but recommended)
- React Router for navigation
- Strapi Client SDK for API calls (@strapi/client)
- TanStack Query (React Query) for data fetching, caching, and state management

BACKEND CONTEXT:
My Strapi v5 backend is deployed at: https://inviting-cheese-ce66d3b9e0.strapiapp.com
Base API URL: https://inviting-cheese-ce66d3b9e0.strapiapp.com/api

IMPORTANT STRAPI DATA STRUCTURE:
Strapi wraps all responses in this structure:
- Collection types: { data: [...], meta: { pagination: {...} } }
- Single types: { data: {...}, meta: {} }
- Each item has: { id: number, documentId: string, attributes: {...} }

CONTENT TYPES AVAILABLE:

1. **Global** (Single Type) - /api/global
   - title, description
   - banner (component), header (component), footer (component)
   - Use for site-wide settings like navigation and footer

2. **Landing Page** (Single Type) - /api/landing-page
   - title, description
   - blocks (dynamic zone with 9 block types)

3. **Articles** (Collection Type) - /api/articles
   - title, description, slug
   - content (rich text)
   - featuredImage (media)
   - author (relation to Author)
   - contentTags (relation to Tags)
   - blocks (dynamic zone - same components as landing page)
   - relatedArticles (relation to other Articles)

4. **Authors** (Collection Type) - /api/authors
   - name, bio, avatar

5. **Tags** (Collection Type) - /api/tags
   - name, slug

DYNAMIC ZONE BLOCKS:
Both Landing Page and Articles use a "blocks" dynamic zone with these components:
- blocks.hero
- blocks.section-heading
- blocks.card-grid
- blocks.content-with-image
- blocks.markdown
- blocks.person-card
- blocks.faqs
- blocks.newsletter
- blocks.featured-articles

REQUIREMENTS:

1. **Setup Strapi Client SDK and API Utilities**

   Install dependencies:
   ```bash
   npm install @strapi/client @tanstack/react-query
   ```

   Create `lib/strapi.ts` with API utility functions:
   ```typescript
   import { strapiClient } from '@strapi/client';

   const client = strapiClient({
     baseURL: 'https://inviting-cheese-ce66d3b9e0.strapiapp.com',
   });

   // 1. Get global settings (single type)
   export async function getGlobal() {
     const response = await client.single('global').find();
     return response.data;
   }

   // 2. Get landing page (single type)
   export async function getLandingPage() {
     const response = await client.single('landing-page').find();
     return response.data;
   }

   // 3. Get all articles with optional search and pagination
   export async function getArticles(params?: {
     search?: string;
     page?: number;
     pageSize?: number;
   }) {
     const { search, page = 1, pageSize = 6 } = params || {};

     const response = await client.collection('articles').find({
       ...(search && {
         filters: {
           $or: [
             { title: { $containsi: search } },
             { description: { $containsi: search } }
           ]
         }
       }),
       pagination: { page, pageSize }
     });

     return {
       articles: response.data,
       pagination: response.meta?.pagination
     };
   }

   // 4. Get single article by slug
   export async function getArticleBySlug(slug: string) {
     const response = await client.collection('articles').find({
       filters: { slug: { $eq: slug } }
     });
     return response.data?.[0] || null;
   }

   // 5. Get articles by tag (bonus feature)
   export async function getArticlesByTag(tagSlug: string, page = 1, pageSize = 6) {
     const response = await client.collection('articles').find({
       filters: {
         contentTags: { slug: { $eq: tagSlug } }
       },
       pagination: { page, pageSize }
     });

     return {
       articles: response.data,
       pagination: response.meta?.pagination
     };
   }
   ```

   Create React Query hooks wrapping these utility functions for data fetching and caching.

   IMPORTANT STRAPI V5 FILTER SYNTAX:
   - Use `filters` object with operators: $eq, $ne, $containsi, $startsWith, $endsWith
   - Multi-field search: use $or array
   - DO NOT use _q parameter (not officially supported in Strapi v5)
   - Pagination: { page: number, pageSize: number }

2. **Landing Page Component (Home Route: /)**
   - Use `useLandingPage()` hook to fetch data
   - Render title and description
   - Dynamically render all blocks from blocks array
   - Create BlockRenderer component that maps __component to React components
   - Component map: { 'blocks.hero': HeroBlock, 'blocks.card-grid': CardGridBlock, ... }

3. **Articles Listing Page (/articles)**
   - Use `useArticles(search, page)` hook
   - Display articles in responsive grid (3 columns desktop, 2 tablet, 1 mobile)
   - Each ArticleCard shows: featuredImage, title, description, author.fullName, contentTags
   - Search input with debouncing (300ms)
   - Pagination controls showing current page, total pages
   - URL query params for state: /articles?search=query&page=2
   - Loading skeleton while fetching
   - Empty state when no articles found

4. **Single Article Page (/articles/:slug)**
   - Use `useArticle(slug)` hook
   - Display: featuredImage, title, description, author (with image), contentTags
   - Render markdown content field
   - Dynamically render blocks using BlockRenderer
   - Related articles grid at bottom
   - 404 page when article not found
   - Loading state and error handling

   Remember flat data structure:
   - article.title, article.featuredImage.url, article.author.fullName, article.author.image.url

5. **Global Layout**
   - Use `useGlobal()` in App component or layout
   - Render header with navigation from global.header
   - Render footer from global.footer
   - Dismissible banner from global.banner
   - Mobile hamburger menu

6. **Dynamic Zone Block Components**
   Create React components for each block type:
   - HeroBlock: heading, subheading, CTA buttons, background image
   - SectionHeadingBlock: heading with description
   - CardGridBlock: grid of cards
   - ContentWithImageBlock: text + image (left/right layouts)
   - MarkdownBlock: markdown renderer
   - PersonCardBlock: avatar, name, bio
   - FAQsBlock: accordion UI
   - NewsletterBlock: email form
   - FeaturedArticlesBlock: article cards grid

   BlockRenderer maps block.__component to appropriate component.

7. **TypeScript Types**
   - Interface for Article, Author, Tag, Global, LandingPage
   - Block type interfaces for all 9 block types
   - Use TypeScript strict mode

CRITICAL CONSTRAINTS:

Data Structure:
- **FLAT DATA - NOT standard Strapi nesting!**
- ✅ Fields are direct: `article.title` (NOT `article.attributes.title`)
- ✅ Relations are direct: `article.author.fullName` (NOT `article.author.data.attributes.fullName`)
- ✅ Media is direct: `article.featuredImage.url` (NOT nested in .data.attributes)
- ✅ Arrays are direct: `article.contentTags.map(tag => tag.title)`

API Rules:
- **NO populate parameters** - backend auto-populates everything
- **NO _q parameter** - use filters with $containsi instead
- Only use `filters` and `pagination` query params
- Strapi Client SDK for all API calls
- TanStack Query for caching and state management

Development:
- TypeScript strict mode
- Tailwind CSS (mobile-first responsive)
- shadcn/ui components
- React Router for navigation
- Proper error boundaries and 404 pages
- Loading skeletons (not spinners)
- Accessibility: alt text, semantic HTML, keyboard navigation
- Dynamic zones: check `block.__component` to render correct component

UNDERSTANDING THE ACTUAL DATA STRUCTURE:

**IMPORTANT:** This Strapi backend returns FLAT data (not the standard nested data.attributes structure).
The middleware transforms responses to be easier to work with.

Example article response (actual structure from API):
```json
{
  "data": [
    {
      "id": 12,
      "documentId": "be9fsxd1j04vgqdsmgq6edsp",
      "title": "How to Improve Your Productivity as a Developer",
      "description": "Developers often struggle with productivity...",
      "slug": "how-to-improve-your-productivity-as-a-developer",
      "content": "## Introduction\n...",
      "createdAt": "2025-08-03T01:13:44.951Z",
      "updatedAt": "2025-08-03T01:18:30.484Z",
      "publishedAt": "2025-08-03T01:18:30.491Z",
      "featuredImage": {
        "id": 7,
        "documentId": "m6k376b2oa58vg2dnu5lsv0i",
        "url": "https://inviting-cheese-ce66d3b9e0.media.strapiapp.com/forest_562bafce29.avif",
        "alternativeText": null
      },
      "author": {
        "id": 2,
        "documentId": "k4510nw2eyzzz0udzxkq0vpm",
        "fullName": "Jacke Brown",
        "bio": "I am a fullstack developer",
        "image": {
          "id": 8,
          "url": "https://inviting-cheese-ce66d3b9e0.media.strapiapp.com/image_of_women_fa698d5653.avif",
          "alternativeText": null
        },
        "articles": [
          { "id": 8, "documentId": "...", "title": "..." }
        ]
      },
      "contentTags": [
        {
          "id": 1,
          "documentId": "ynz9y1i6fgnony4pevrta8vw",
          "title": "Tech",
          "description": "Articles about tech"
        }
      ],
      "blocks": [],
      "relatedArticles": [
        {
          "id": 11,
          "title": "The Benefits of Headless CMS for Modern Websites",
          "slug": "the-benefits-of-headless-cms-for-modern-websites",
          "featuredImage": { "id": 7, "url": "...", "alternativeText": null },
          "author": { "fullName": "Jacke Brown", "bio": "..." }
        }
      ]
    }
  ],
  "meta": {
    "pagination": { "page": 1, "pageSize": 25, "pageCount": 1, "total": 6 }
  }
}
```

**How to access this data:**
- Article title: `article.title` (NOT article.attributes.title)
- Featured image: `article.featuredImage.url`
- Author name: `article.author.fullName`
- Author avatar: `article.author.image.url`
- Tags: `article.contentTags.map(tag => tag.title)`
- Blocks: `article.blocks.map(block => { /* check block.__component */ })`
- Related articles: `article.relatedArticles[0].title`

**Key differences from standard Strapi:**
- ✅ Fields are directly on the object (no `.attributes` wrapper)
- ✅ Relations are direct objects/arrays (no `.data` wrapper)
- ✅ Media fields are direct objects (no `.data.attributes.url`)
- ✅ This makes the data much easier to work with!

SAMPLE API ENDPOINTS TO TEST:
- GET https://inviting-cheese-ce66d3b9e0.strapiapp.com/api/global
- GET https://inviting-cheese-ce66d3b9e0.strapiapp.com/api/landing-page
- GET https://inviting-cheese-ce66d3b9e0.strapiapp.com/api/articles?pagination[page]=1&pagination[pageSize]=6
- GET https://inviting-cheese-ce66d3b9e0.strapiapp.com/api/articles?filters[slug][$eq]=why-java-script-is-still-the-most-popular-programming-language

Note: All relations, images, and nested data are automatically populated AND flattened via backend middleware!

OPENAPI SPECIFICATION:
<openapi-spec>
[The full specification.json file content will be pasted here - available in the project root after running: yarn strapi openapi generate]
</openapi-spec>

SAMPLE API RESPONSES:

<sample-global-response>
[Paste actual response from /api/global - all relations auto-populated by middleware]
</sample-global-response>

<sample-landing-page-response>
[Paste actual response from /api/landing-page - all blocks auto-populated by middleware]
</sample-landing-page-response>

<sample-articles-response>
[Paste actual response from /api/articles - all relations auto-populated by middleware]
</sample-articles-response>

<sample-single-article-response>
[Paste actual response from /api/articles?filters[slug][$eq]=YOUR_SLUG - all nested data auto-populated]
</sample-single-article-response>

DELIVERABLES - Build the following:

Project Structure:
- `/lib/strapi.ts` - API utility functions (code provided above)
- `/hooks/useStrapi.ts` - React Query hooks wrapping the API utilities
- `/types/strapi.ts` - TypeScript interfaces for all content types
- `/components/blocks/` - 9 block components for dynamic zones
- `/components/BlockRenderer.tsx` - Dynamic zone renderer
- `/components/ArticleCard.tsx` - Reusable article card
- `/pages/` - Landing, Articles, SingleArticle pages
- `/components/Layout.tsx` - Global header, footer, banner

Features:
✅ Landing page with dynamic blocks rendering
✅ Articles listing with search (debounced), pagination, loading states
✅ Single article page with blocks, related articles, 404 handling
✅ Global navigation and footer from Strapi
✅ Mobile responsive (hamburger menu, responsive grids)
✅ TypeScript strict mode throughout
✅ TanStack Query for caching and optimistic updates
✅ Skeleton loaders (not spinners)
✅ Proper error boundaries and error states
✅ Accessibility compliant (alt text, semantic HTML)
```

## Why Such a Detailed Prompt? Understanding Context in AI Development

You might be wondering: "Why did we create such a long, detailed prompt? Can't we just tell Lovable to 'build a blog with Strapi'?"

Here's the reality: **The quality of AI-generated code is directly proportional to the quality and specificity of your prompt.**

### The Context Problem with AI Tools

AI coding assistants like Lovable.dev are incredibly powerful, but they have a fundamental limitation: they don't know anything about *your* specific backend unless you tell them. They know:

✅ General React patterns
✅ Standard TypeScript syntax
✅ Common libraries like TanStack Query
✅ Generic REST API patterns

But they **don't know**:

❌ Your Strapi backend's specific URL
❌ Your custom middleware that flattens data
❌ Your exact content type structure
❌ That you're using Strapi Client SDK instead of fetch
❌ Your filter syntax requirements ($containsi vs _q)

### What Happens Without Proper Context

If you just say "build a blog with Strapi," the AI will:

1. **Hallucinate API endpoints** - It might create `/api/posts` when you have `/api/articles`
2. **Use wrong data access patterns** - Access `article.attributes.title` when your middleware flattened it to `article.title`
3. **Generate incompatible code** - Use the `_q` search parameter that isn't officially supported
4. **Miss critical features** - Not implement dynamic zones or block rendering
5. **Create mock data** - Build a working UI with fake data that never connects to your real Strapi

The result? You spend hours debugging why "it doesn't work" when the real issue is the AI didn't know your actual API structure.

### The Strapi SDK: Why We Provided Implementation

You might notice we provided complete code for the Strapi Client SDK utilities but didn't show React Query implementations. This is intentional:

**Strapi-Specific Knowledge (We Provide):**
```typescript
// This is Strapi-specific - AI needs the exact syntax
const client = strapiClient({
  baseURL: 'https://your-strapi.strapiapp.com',
});

// How to use client.single() vs client.collection()
const response = await client.single('global').find();
const articles = await client.collection('articles').find({
  filters: { slug: { $eq: slug } }
});
```

**General React Knowledge (AI Knows):**
```typescript
// This is standard React Query - AI knows this pattern
const { data, isLoading } = useQuery({
  queryKey: ['articles'],
  queryFn: getArticles
});
```

We provided the Strapi SDK code because:

1. **It's library-specific** - Lovable needs to know the exact API of `@strapi/client`
2. **Filter syntax is non-obvious** - Using `$containsi` vs `_q` isn't something AI would guess correctly
3. **Your backend is unique** - The API URL, endpoint names, and data structure are specific to your project
4. **Custom middleware matters** - Standard Strapi nests data differently than your flattened structure

### The Right Balance: Specificity vs Flexibility

Our prompt strikes a balance:

**Highly Specific (Strapi Context):**
- ✅ Complete Strapi Client SDK utility functions
- ✅ Actual API URLs and endpoints
- ✅ Real response structure examples
- ✅ Filter operators and syntax rules
- ✅ Content type schemas and relationships
- ✅ Dynamic zone component names

**Appropriately General (React Patterns):**
- ✅ "Create React Query hooks" (not the exact implementation)
- ✅ "Responsive grid layout" (not the exact Tailwind classes)
- ✅ "Loading skeletons" (not the exact skeleton component)
- ✅ "Debounced search" (not the exact debounce logic)

This approach gives Lovable:
- **Concrete facts** about your Strapi backend it couldn't possibly know
- **Freedom to use best practices** for React patterns it already knows well

### Why Real API Responses Matter

Including actual JSON responses from your API is crucial:

```json
{
  "data": [{
    "id": 12,
    "title": "Article Title",        // ← Flat structure!
    "author": {                       // ← Direct object, not nested
      "fullName": "Jane Doe"
    },
    "featuredImage": {                // ← Direct media object
      "url": "https://..."
    }
  }]
}
```

This shows Lovable:
1. **Exact field names** - `fullName` not `name`, `featuredImage` not `coverImage`
2. **Data nesting level** - Flat, not `data.attributes.title`
3. **Null handling** - Which fields might be null (like `alternativeText`)
4. **Array structures** - How relations like `contentTags` are returned

Without these examples, the AI would generate code for *standard* Strapi (nested structure), which would fail against your *custom* middleware.

### The Cost of Vague Prompts

Let's compare:

**Vague Prompt:**
> "Build a blog frontend for my Strapi backend"

**Result:**
- Generic blog with fake data
- Wrong API structure assumptions
- Hours of manual debugging
- Frustration and re-work

**Specific Prompt (What We Created):**
> Includes: Backend URL, SDK code, filter syntax, real responses, content types, block components, flat data structure

**Result:**
- Working blog connected to real Strapi
- Correct data access patterns
- Minimal debugging needed
- Fast iteration on features

The detailed prompt takes 5 minutes to prepare but saves hours of debugging.

### Key Takeaway: Context is Currency

When working with AI coding tools:

1. **Provide what only you know** - Your backend specifics, API structure, custom logic
2. **Let AI handle what it knows** - Standard React patterns, UI components, common libraries
3. **Show, don't just tell** - Include actual API responses, not just descriptions
4. **Be explicit about deviations** - If you've customized Strapi (like we did), make it obvious

The more accurate context you provide about Strapi-specific details, the better Lovable can generate code that actually works with your backend.

### Step 6: Build with Lovable.dev

1. **Create a new project** in Lovable.dev
2. **Copy the complete prompt above** (the entire ```txt block)
3. **Paste the sample API responses** into the placeholders:
   - Get actual responses from your Strapi endpoints
   - Replace `[Paste actual response from /api/global...]` with real JSON
   - Do this for global, landing-page, articles, and single article
4. **Paste the OpenAPI spec** (optional but recommended)
   - Run `yarn strapi openapi generate` in your server
   - Copy contents of `specification.json`
   - Paste into the `<openapi-spec>` section
5. **Submit the prompt to Lovable**
6. **Let Lovable generate your entire frontend** - It will create all files, components, hooks, and pages
7. **Iterate with focused prompts** if needed:
   - "Add infinite scroll instead of pagination"
   - "Improve mobile navigation UX"
   - "Add article reading time estimate"

### Step 7: Test and Refine

As Lovable builds your application:

1. **Test API connectivity** - Ensure CORS is properly configured in Strapi
2. **Verify data rendering** - Check that all fields display correctly
3. **Test error handling** - Try invalid slugs, network failures, etc.
4. **Check responsive design** - Test on mobile, tablet, and desktop viewports
5. **Optimize performance** - Look for unnecessary re-renders or API calls

### Step 8: Deploy Your Frontend

Lovable.dev provides integrated deployment options:

1. **Click the Deploy button** in Lovable.dev
2. **Configure environment variables** (your Strapi API URL)
3. **Deploy to production** - Lovable typically uses Vercel or similar platforms

## Best Practices for AI-Powered Development with Strapi

### 1. Always Include Real API Responses

Don't just provide the OpenAPI spec—include actual API responses from your endpoints. This ensures the AI understands your specific data structure (especially if you've customized it like we did with middleware).

Fetch real responses:
```bash
# Get actual responses from your deployed Strapi
curl https://your-strapi.strapiapp.com/api/global
curl https://your-strapi.strapiapp.com/api/articles
```

### 2. Be Explicit About Custom Transformations

If you've added middleware that transforms Strapi's default response structure (like flattening data), make this VERY clear in your prompt. Standard Strapi uses nested `data.attributes`, but our middleware flattens this.

### 3. Update Your Spec When Content Types Change

Whenever you modify Strapi content types:

```bash
yarn strapi openapi generate
```

Then update your Lovable prompt with the new spec.

If your Strapi has custom middleware (like our auto-populate and data-flattening middleware), document this clearly. In our case:
- Middleware auto-populates all relations
- Middleware flattens the response (no `.attributes` nesting)
- This makes frontend code cleaner but deviates from standard Strapi

### 5. Test with Real Data Early

Don't wait until the frontend is "done" to test with real Strapi data:
1. Get Lovable to generate the basic structure
2. Test API connectivity immediately
3. Verify data rendering with actual content
4. Iterate on styling and features once data flow works

## Common Pitfalls and How to Avoid Them

### Pitfall 1: Vague Prompts Lead to Generic Code

**Problem**: Asking Lovable to "build a blog" without context results in generic code that doesn't match your API.

**Solution**: Provide OpenAPI spec, real API responses, and specific requirements about data structure and features.

### Pitfall 2: Assuming Standard Strapi Structure

**Problem**: AI generates code expecting standard nested `data.attributes` structure, but your middleware has flattened it.

**Solution**: Include actual API responses in your prompt and explicitly state any custom transformations. Show examples of how to access data: `article.title` not `article.attributes.title`.

### Pitfall 3: Missing CORS Configuration

**Problem**: Your frontend can't connect to Strapi due to CORS restrictions.

**Solution**: Configure CORS in your Strapi project (`config/middlewares.ts`):

```typescript
export default [
  // ...other middleware
  {
    name: "strapi::cors",
    config: {
      enabled: true,
      origin: ["http://localhost:3000", "https://your-frontend-domain.com"],
    },
  },
];
```

### Pitfall 4: Over-fetching or Under-fetching Data

**Problem**: Not populating relations results in missing data, or populating everything causes performance issues.

**Solution**: Be specific about which relations to populate:

```
For article listing: populate=coverImage,category,author
For article detail: populate=*
```

### Pitfall 5: Runaway API Calls

**Problem**: AI-generated code may create infinite loops or excessive API calls.

**Solution**: Implement debouncing, proper dependency arrays in useEffect, and rate limiting:

```typescript
// Add to your prompt:
"Implement search with 300ms debounce to prevent excessive API calls.
Ensure useEffect hooks have proper dependency arrays to avoid infinite loops.
Add request caching where appropriate."
```

## Prompting Best Practices for Lovable.dev

When working with Lovable.dev (similar to V0), follow these best practices:

### 1. Define the Role and Context

```txt
You are a senior frontend developer experienced with React, TypeScript, and consuming Strapi APIs.
```

### 2. Specify Your Tech Stack

```txt
TECH STACK:
- React 18+ with TypeScript
- Tailwind CSS for styling
- React Router for navigation
- Axios or Fetch for API calls
```

### 3. Provide Complete API Context

```txt
BACKEND:
- Strapi v5 headless CMS
- Base URL: https://your-strapi.com
- Authentication: None required (public API)
- OpenAPI spec provided below
```

### 4. List Specific Requirements

```txt
REQUIREMENTS:
1. Feature one with specific details
2. Feature two with acceptance criteria
3. Feature three with edge cases to handle
```

### 5. Set Constraints

```txt
CONSTRAINTS:
- Must use the exact API structure from OpenAPI spec
- Mobile-first responsive design
- Accessibility (WCAG 2.1 AA compliance)
- No client-side state management library (use React Context if needed)
```

### 6. Include Data Structures

```txt
<openapi-spec>
[Your full OpenAPI specification]
</openapi-spec>

<sample-data>
[Real API response example]
</sample-data>
```

## Example Workflow: Start to Finish

Let me walk you through a complete example:

### 1. Create Strapi Project

```bash
npx create-strapi-app@latest blog-backend --quickstart
```

### 2. Create Content Types

- Article (title, slug, content, coverImage, author, category)
- Author (name, bio, avatar)
- Category (name, slug)

### 3. Add Sample Content

Create 5-10 articles with various categories and authors.

### 4. Generate OpenAPI Spec

```bash
npx strapi openapi generate
```

### 5. Deploy to Strapi Cloud

```bash
strapi deploy
```

### 6. Copy OpenAPI Spec and Sample Response

```bash
# Get OpenAPI spec
cat openapi.json

# Get sample response (using curl or browser)
curl "https://your-strapi.strapiapp.com/api/articles?populate=*"
```

### 7. Create Lovable.dev Project

Paste your comprehensive prompt with OpenAPI spec and sample data.

### 8. Generate Initial Code

Let Lovable build the foundation.

### 9. Iterate with Specific Prompts

```txt
"Add a sticky header that changes background on scroll"
"Implement breadcrumb navigation on article detail pages"
"Add social share buttons (Twitter, Facebook, LinkedIn)"
"Create a 'Read More' section with related articles"
```

### 10. Deploy

Use Lovable's integrated deployment or export the code to your preferred hosting.

## Lessons Learned

After building multiple projects with this approach, here are key insights:

### OpenAPI Specifications Eliminate Guesswork

Providing the complete API schema upfront prevents the AI from hallucinating endpoints or field names. The code generated is immediately compatible with your backend.

### Backend-First Reduces Rework

When your data model is solidified before building the UI, you avoid constant refactoring. The content structure informs the component architecture naturally.

### Iterative Prompting Beats One-Shots

While comprehensive initial prompts are important, breaking down features into smaller, focused follow-up prompts produces better results and allows for course correction.

### Real Data Reveals Edge Cases

Testing with actual content from Strapi reveals issues that mock data hides: long titles, missing images, empty states, malformed content, etc.

### CORS and Permissions Trip Everyone Up

Always configure CORS properly and verify public API access in Strapi before blaming the frontend code.

## Moving Beyond Lovable.dev

Once you have a solid prototype from Lovable.dev, you might want to:

1. **Export the code** and continue development locally
2. **Use Claude Code** for advanced refactoring and optimization
3. **Add testing** with Jest and React Testing Library
4. **Implement CI/CD** with GitHub Actions
5. **Add advanced features** like authentication, comments, etc.

This is where tools like Claude Code shine—you can have autonomous agents work on specific features while you maintain oversight.

## Conclusion

Building a frontend with Lovable.dev for your Strapi backend is remarkably efficient when you leverage OpenAPI specifications. The key is providing comprehensive context upfront: your API schema, sample data, technical constraints, and specific requirements.

Remember these core principles:

- **Backend-first with Strapi** gives you a solid foundation
- **OpenAPI specs** eliminate AI hallucinations and ensure type safety
- **Detailed prompts** with real data produce better results
- **Iterative refinement** beats trying to generate everything at once
- **Human oversight** remains critical—review, test, and guide the AI

AI tools like Lovable.dev aren't replacing developers—they're amplifying our ability to build faster and experiment more freely. But you still need to understand your data model, API contracts, and application architecture to use them effectively.

Start with a clear Strapi backend, generate that OpenAPI spec, craft a comprehensive prompt, and let Lovable.dev accelerate your frontend development. Just remember: the AI is your collaborator, not your replacement.

---

**Want to learn more?** Join me Mon-Fri at 12:30 PM CST for Strapi's open office hours on [Discord](https://discord.com/invite/strapi), or find me @codingthirty on X.

**Resources:**

- [Lovable.dev](https://lovable.dev)
- [Strapi Cloud](https://strapi.io/cloud)
- [Strapi Documentation](https://docs.strapi.io)
- [OpenAPI Specification](https://swagger.io/specification/)
- [Claude Code](https://claude.ai/code)
