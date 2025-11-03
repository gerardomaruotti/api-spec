# PoliTO API Spec
The OpenAPI specification for the REST API of Politecnico di Torino.

## Project Structure

The specification is organized into **domain-specific indexes** to minimize merge conflicts and enable parallel development:

```
src/
├── index.yaml                 # Unified entry point (DO NOT edit directly)
├── index-common.yaml          # Common domain (auth, profile, news, places, etc.)
├── index-students.yaml        # Students domain (exams, lectures, tickets, etc.)
├── index-faculty.yaml         # Faculty domain (calendar, courses, exams, etc.)
├── paths/
│   ├── common/                # Common API endpoints
│   ├── students/              # Student-specific endpoints
│   └── faculty/               # Faculty-specific endpoints
└── components/
    ├── shared/                # Cross-domain shared (parameters, responses, security)
    ├── common-components.yaml # Common domain schemas & parameters
    ├── students-components.yaml # Students domain schemas & parameters
    └── faculty-components.yaml # Faculty domain schemas & parameters
```

### Development Approach

- **Work on domain-specific indexes**: Edit `index-{domain}.yaml` for your domain
- **DO NOT edit `src/index.yaml`**: It's the aggregator and should remain stable
- **Generated bundles**: `openapi.yaml` and `dist/*` are ignored by Git

## Prerequisites

- Node.js 18 (match the CI environment)
- npm (ships with Node.js)
- Optional: Docker and Prism for documentation and mocking

Install dependencies once per clone:

```bash
npm ci
```

## Development Workflow

### For Domain-Specific Changes

1. **Choose your domain** and edit the corresponding index:
   - Common APIs → `src/index-common.yaml`
   - Students APIs → `src/index-students.yaml`
   - Faculty APIs → `src/index-faculty.yaml`

2. **Bundle and validate your domain**:
   ```bash
   # For common domain
   npm run bundle:common && npm run validate:common

   # For students domain
   npm run bundle:students && npm run validate:students

   # For faculty domain
   npm run bundle:faculty && npm run validate:faculty
   ```

3. **Validate the unified spec**:
   ```bash
   npm run bundle:verify
   ```
   This ensures your changes integrate correctly with other domains.

4. **Commit your changes**:
   ```bash
   git add src/index-{domain}.yaml
   git add src/paths/{domain}/
   git add src/components/{domain}-components.yaml  # if modified
   git commit -m "feat: add emergency endpoints to common domain"
   ```
   Note: `openapi.yaml` and `dist/` are gitignored.

5. **Open a pull request**. CI validates all domain specs and the unified bundle.

### For Changes Across Multiple Domains

If your work spans multiple domains (e.g., adding a shared schema):

```bash
# Bundle and validate all domains + unified
npm run verify:all
```

### Useful npm Scripts

| Command                  | Description                                         |
|--------------------------|-----------------------------------------------------|
| `npm run bundle:common`  | Bundle common domain spec                           |
| `npm run bundle:students`| Bundle students domain spec                         |
| `npm run bundle:faculty` | Bundle faculty domain spec                          |
| `npm run bundle:all`     | Bundle all domains + unified spec                   |
| `npm run validate:common`| Validate common domain bundle                       |
| `npm run validate:students`| Validate students domain bundle                   |
| `npm run validate:faculty`| Validate faculty domain bundle                     |
| `npm run validate:all`   | Validate all domain bundles + unified               |
| `npm run verify:all`     | Bundle and validate everything (pre-commit check)   |
| `npm run bundle`         | Bundle unified spec (backward compatibility)        |
| `npm run validate`       | Validate unified bundle                             |
| `npm run bundle:verify`  | Bundle + validate unified (quick check)             |

## How to obtain a human-readable interface
If you are accustomed to using Postman, you can just import the .yaml file containing the specification.

Alternatively, you can start a Docker container of swagger-ui directly on your computer by running: 
```bash
docker-compose up
``` 
By default, the web interface will start on port 8080.

## About OpenAPI
These definitions provide a single point of truth that can be used end-to-end:

- **Planning** Shared during product discussions for planning API functionality
- **Implementation** Inform engineering during development
- **Testing** As the basis for testing or mocking API endpoints
- **Documentation** For producing thorough and interactive documentation
- **Tooling** To generate server stubs and client SDKs.

## Running a mock API
We suggest using [Prism](https://github.com/stoplightio/prism) to run a mock version of this API.

After installing it, you can start a mock server with the following command:
```bash
prism mock -h YOUR-LOCAL-IP ./openapi.yaml
```
You only need to specify your IP if you want them to be accessible from other devices in the same network (e.g. while working on [@polito/students-app](https://github.com/polito/students-app), since it will typically run on a distinct device).

## Resources

- [OAS3 Specification](http://spec.openapis.org/oas/v3.0.3)
- [OAS3 Examples](https://github.com/OAI/OpenAPI-Specification/tree/master/examples/v3.0)
- [OpenAPI Multi-Document Pattern](https://spec.openapis.org/oas/v3.1.0#relative-references-in-uris)
