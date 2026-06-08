
# Multi-Stage Build

A Multi-Stage Build is a Docker technique that uses multiple FROM statements within a single Dockerfile to separate your build environment from your runtime environment.
It allows you to install heavy compilers, download dependencies, and build your application in early stages, and then copy only the compiled artifacts or production files into a clean final stage. This isolates your source code and development tools, drastically reducing image size and eliminating security vulnerabilities before deploying to production.
------------------------------
## The Problem vs. The Multi-Stage Solution
Imagine a standard Go application. To build it, you need the full Go compiler suite.
## The Old, Unoptimized Way (Single Stage)

FROM golang:1.24WORKDIR /appCOPY . .RUN go build -o myapp .CMD ["./myapp"]


* Result: The final image is around 800 MB. It contains the Go compiler, package managers, testing tools, and source files—none of which are needed to run the single compiled binary file.

## The Multi-Stage Way (Optimized)

# Stage 1: The Builder StageFROM golang:1.24 AS builderWORKDIR /appCOPY . .RUN CGO_ENABLED=0 GOOS=linux go build -o myapp .
# Stage 2: The Final Production Stage (Can be Distroless, Alpine, or Slim)FROM gcr.io/distroless/static-debian12WORKDIR /COPY --from=builder /app/myapp /myappCMD ["/myapp"]


* Result: The final image drops to around 20 MB. The heavy golang:1.24 image layers are discarded completely, and only the single myapp file is transferred to the secure production container via the COPY --from=builder instruction.

------------------------------
## Key Mechanics of Multi-Stage Builds

   1. Naming Stages (AS <name>): You tag an early phase by adding AS builder to the FROM line. This lets you reference that specific stage later in the file.
   2. Artifact Extraction (COPY --from): The core magic happens with COPY --from=builder /source/path /destination/path. This allows you to pull files out of previous stages directly into your active layer stack.
   3. Layer Discarding: When the build finishes, Docker only saves the very last stage as your final image. All the intermediate filesystem layers generated during the building phases are automatically left behind on your host machine as untagged build cache layers.

------------------------------
## Another Real-World Example: Node.js / React
Multi-stage builds are critical for modern frontend development where thousands of heavy development tools (node_modules) are needed to minify code, but only static HTML/JS files are deployed to a production web server.

# Stage 1: Install packages and build static web filesFROM node:22-alpine AS compilerWORKDIR /buildCOPY package*.json ./RUN npm installCOPY . .RUN npm run build  # Generates a tiny folder named './dist'
# Stage 2: Serve the production files using an ultra-light Nginx serverFROM nginx:alpineCOPY --from=compiler /build/dist /usr/share/nginx/htmlEXPOSE 80CMD ["nginx", "-g", "daemon off;"]

## Key Advantages for Production

* Zero Attack Surface: Hackers cannot look for language build suites or package installers because those layers are dropped entirely.
* Separation of Secrets: If you require private SSH keys or tokens to download internal enterprise modules during compilation, you can use them in Stage 1. Because Stage 1 is discarded, those credentials will never be exposed in your public production image history.
* High Cache Efficiency: Docker caches each stage independently. If your application code changes but your third-party dependencies do not, Docker skips the dependency resolution layers entirely during the next build cycle.

If you want to practice optimizing your own build flow, what programming language runtime or framework does your current application run on?

