
# Difference between bind mount and volume

What is the difference between bind mount and volume?
Volume: Managed by Docker.
Bind Mount: Uses a host filesystem path.

Example:

```bash
docker run -v myvolume:/data nginx
docker run -v /host/path:/data nginx
```