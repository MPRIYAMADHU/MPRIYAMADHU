---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Setting Up the Spring Boot Application:

@Configuration
@EnableWebSecurity 
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    // Configuration for user authentication
    
}
//This section instructs us to configure Spring Security for user authentication and authorization.

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Create REST endpoint for document uploads:

@RestController
@RequestMapping("/api/documents")
public class DocumentController {
    @Autowired
    private DocumentService documentService;

    @PostMapping("/upload")
    public ResponseEntity<String> uploadDocument(@RequestParam("file") MultipartFile file, Principal principal) {
        // Handle document upload and storage
        // ...
        return ResponseEntity.ok("Document uploaded successfully.");
    }
}
//This section guides us to create a REST controller in Spring Boot to handle document uploads and store them in MongoDB.

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Save document metadata in MongoDB:

@Document(collection = "documents")
public class Document {
    @Id
    private String documentId;
    private String documentName;
    private byte[] document;
    private String uploadUser;
    private Date uploadDate;
    
    // Getters and setters
}

------------------------------------------
public interface DocumentRepository extends MongoRepository<Document, String> {
    // Custom query methods if needed
}
//In this part, it's suggested to define a MongoDB entity class for documents and create a repository interface for data access.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Store document data:

Document document = new Document();
document.setDocumentName(file.getOriginalFilename());
document.setDocument(file.getBytes());
document.setUploadUser(principal.getName());
document.setUploadDate(new Date());
documentRepository.save(document);    
//Within the DocumentController, this part focuses on saving the actual document data into MongoDB.

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Create an Angular component:

import { Component, OnInit } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Component({
  selector: 'app-document-list',
  templateUrl: './document-list.component.html',
  styleUrls: ['./document-list.component.css']
})
export class DocumentListComponent implements OnInit {
  documents: any[];

  constructor(private http: HttpClient) { }

  ngOnInit() {
    // Fetch documents from the backend
    this.http.get<any[]>('/api/documents/list').subscribe(data => {
      this.documents = data;
    });
  }
}
//This part recommends creating an Angular component responsible for displaying the document list using Angular Material's data table.

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Display documents in Angular Material table:

<table mat-table [dataSource]="documents">
  <!-- Define table columns and rows -->
</table> 
//In this step, it advises us to use Angular Material's mat-table component to display the documents in the component's HTML file.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Implement delete functionality:

<button mat-icon-button (click)="deleteDocument(document)">Delete</button>
//This section suggests adding a delete button for each document in the Angular component's HTML file.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Handle document deletion in the backend:

@DeleteMapping("/{documentId}")
public ResponseEntity<String> deleteDocument(@PathVariable String documentId) {
    // Handle document deletion from MongoDB
    // ...
    return ResponseEntity.ok("Document deleted successfully.");
}
//In the DocumentController, this part involves creating a REST endpoint to handle document deletion from MongoDB.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Update the document list:

deleteDocument(document: any) {
  // Send delete request to the backend
  this.http.delete(`/api/documents/${document.documentId}`).subscribe(() => {
    // Update the documents list after deletion
    this.documents = this.documents.filter(d => d.documentId !== document.documentId);
  });
}    
//Within the Angular component, this step describes updating the document list after a successful deletion.
