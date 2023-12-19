---
layout: post
title: "Image upload and save into the database"
---
I lost all my nerves while writing code for my project but as programmer there is no other option than keep working on.
I'm Lydia Winkler and I started a project called *GigglyGraphics* during my participation at the Java programming course at *everyone codes*. GigglyGraphics is for artists who wants to improve their skills by repeating a selfmade list of tasks. One of the features is to upload an Image of their drawings after finishing the task. And this blog post is about my experience with this feature.

# Requirements

So, to provide a bit of background, the requirements for my app were as follows:
- The user should be able to add and delete single tasks as well as a whole list of tasks.
- The user should be able to archive and reactivate single tasks wether the task is needed or not.
- The user should be able to choose if he/she wants the next task of the list or a random one.
- The user should be able to add a snapshot to the task.
    *Snapshots includes the Image, a selfrating and a field where the user can enter the mistakes he/she recogniced.*
- The User should be able to view the snapshot as well as the task itself.
- All Images should be displayed in a galery and after clicking on a single image they should be navigated to the view of the snapshot where the image is included.

# Working process

First I had to choose between saving the image into the database and saving it into a file system and only store the filepath in the database.
I'm completely new to this so I had to research a bit but got the final answer really quick:
- Storing the image itself in the database is completely against best practice.
So I went with the filesystem starting with creating a folder where all the images should be stored.

The next step was to create a new Entity called *ImageData* with the following code:
```java
@Entity
@AllArgsConstructor
@NoArgsConstructor
@Getter
@Setter
public class ImageData {
    @Id
    @GeneratedValue
    private Long id;

    private String name;
    private String filePath;

    @OneToOne(mappedBy = "image")
    @JsonBackReference
    private Snapshot snapshot;

    public ImageData(String name, String filePath) {
        this.name = name;
        this.filePath = filePath;
    }
```

Every Snapshot has only one image and vice versa so I implemented a OneToOne-Relationship. To avoid *Infinite Recursion* I added the JsonBackReference annotation to the snapshotproperty.

So let's move on to the ImageDataService.
```java
@Service
public class ImageDataService {
    private final ImageDataRepository repository;

    //Path won't be hardcoded in further progress. -> Will be implemented in the application properties.
    private final Path folderPath = Path.of("/Users/lydia/IdeaProjects/FileSystem_Images/");

    public ImageDataService(ImageDataRepository repository) {
        this.repository = repository;
    }


    public ImageData uploadAndSave(MultipartFile file) throws IOException {
        if (!file.isEmpty()) {

            String randomID = UUID.randomUUID().toString();
            String originalFileName = file.getOriginalFilename();
            String uniqueFileName = randomID.concat(originalFileName.substring(originalFileName.lastIndexOf(".")));

            String filePath = "/Users/lydia/IdeaProjects/FileSystem_Images/" + uniqueFileName;
            file.transferTo(Path.of(filePath));
            return repository.save(new ImageData(uniqueFileName, filePath));

        } else {
            throw new RuntimeException("File is empty");
        }
    }
```
This is the first time I was confronted with the *MultipartFile*
(Todo: add short MultipartFile explanation)
(Add some Expections + Solution)


