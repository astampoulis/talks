# Makam talk at Originate NYC

Given on Tuesday, October 23rd, 2018.

## Dependencies

- `docker`

## Dev build instructions

- `./build.sh devlocal`
- `docker-compose up`
- Visit [http://localhost:8000/devlocal.html](http://localhost:8000/devlocal.html)

## Final build instructions

- `./build.sh offline`
- `docker-compose up`
- Visit [http://localhost:8000/offline.html](http://localhost:8000/offline.html)

This version only depends on local URLs, which is useful for the actual presentation.
