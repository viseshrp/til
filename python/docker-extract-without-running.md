# Extract files from a docker container without running it

```python
import docker
from docker.errors import APIError
from pathlib import Path
import tarfile

def pull_and_extract_files(image_url, output_directory):
    """
    Pulls a Docker image from a registry URL, extracts files from the /Nexus/CEI folder,
    and saves them to a specified directory on the disk.

    :param image_url: URL of the Docker image (e.g., "registry_url/repository:tag")
    :param output_directory: Directory where the files will be saved
    """
    client = docker.from_env()

    try:
        # Pull the image from the registry
        print(f"Pulling image {image_url}...")
        image = client.images.pull(image_url)
        print("Image pulled successfully.")

        # Create a temporary container from the image
        print("Creating temporary container...")
        container = client.containers.create(image.id)

        try:
            # Copy the /Nexus/CEI folder from the container
            print("Copying files from /Nexus/CEI...")
            tar_stream, _ = container.get_archive("/Nexus/CEI")

            # Ensure the output directory exists
            output_path = Path(output_directory)
            output_path.mkdir(parents=True, exist_ok=True)

            # Save the tar archive
            tar_file_path = output_path / "cei_files.tar"

            with tar_file_path.open("wb") as tar_file:
                for chunk in tar_stream:
                    tar_file.write(chunk)

            print(f"Files saved as tar archive: {tar_file_path}")
            print("Extracting tar archive...")

            # Extract the tar archive
            with tarfile.open(tar_file_path) as tar:
                tar.extractall(path=output_path)

            print(f"Files extracted to: {output_path}")

        finally:
            # Clean up the container
            print("Removing temporary container...")
            container.remove()

    except APIError as e:
        print(f"Docker API error: {e}")
    except FileNotFoundError as e:
        print(f"File not found: {e}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")
    finally:
        client.close()

# Example usage
if __name__ == "__main__":
    image_url = "your_registry_url/your_repository:your_tag"
    output_dir = "/path/to/output_directory"
    pull_and_extract_files(image_url, output_dir)
```
