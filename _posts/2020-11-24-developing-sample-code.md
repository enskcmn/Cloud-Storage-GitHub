---
title: Integrating Cloud Storage SDK with Auth Service
description: 15
---

<p><strong>1. Locate following line in LoginFragment.</strong></p>
<pre><div id="copy-button10" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>//TODO Login operation
<span class="pln">
</span></code></pre>
<p><strong>2. initiate Auth service to user login. Without login, system will not allow the user for any operation on storage</strong></p>
<pre><div id="copy-button11" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>    
  val factoryOptions = val mAuthParam = HuaweiIdAuthParamsHelper(HuaweiIdAuthParams.DEFAULT_AUTH_REQUEST_PARAM)
        .setIdToken()
        .setAccessToken()
        .setProfile()
        .createParams()
    val mAuthManager = HuaweiIdAuthManager.getService(context, mAuthParam)
    //start login activity
    startActivityForResult(
        mAuthManager.signInIntent,
        REQUEST_SIGN_IN_LOGIN_CODE
    )
<span class="pln">
</span></code></pre>
<p><strong>3. Apply for read and write permission in run time.</strong></p>
<pre><div id="copy-button11" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>    
  //TODO Read and Write permissions should be granted from user to be able to reach storage
private val mPermissions = arrayOf(
    Manifest.permission.WRITE_EXTERNAL_STORAGE,
    Manifest.permission.READ_EXTERNAL_STORAGE
)
<span class="pln">
</span></code></pre>
<p><strong>4. Locate following line to initiate AGCCloudStorageManagement</strong></p> 
<pre><div id="copy-button12" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code> //TODO  initialize AGCCloudStorageManagement
<span class="pln">
</span></code></pre>
<p><strong>5. Initiate the CloudStorageManagement service as following.</strong></p>
<pre><div id="copy-button13" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code> 
storageManagement = AGCStorageManagement.getInstance()
 <span class="pln">
</span></code></pre>
<p><strong>6. Create storage reference whether under your specific folder (i.e. ‘images/’) or without specifying folder name (default: ‘/’). 
Apply the listAll method of this reference to get all files from storage.</strong></p>
<pre><div id="copy-button14" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code> //TODO get all the file list from storage area
fun listAllFile(): List<StorageReference> {
    var ref = storageManagement.getStorageReference("images/")
    var fileStorageList = ref.listAll()
    return Tasks.await(fileStorageList).fileList
}
<span class="pln">
</span></code></pre>
<p><strong>7. Create storage reference under specified folder and apply to putFile method of this reference with the exact path of chosen file.</strong></p>
<pre><div id="copy-button15" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code> //TODO Upload a file from your phone storage
  fun uploadFile(uri:String, fileExtension: String, listener: UploadFileViewModel) {
    var ref = storageManagement.storageReference.child(
        "images/" + System.currentTimeMillis()
                + "." + fileExtension
    )
    var uploadTask = ref.putFile(File(uri))
    uploadTask.addOnSuccessListener {
        Log.i("TAG", "uploadFile: $it")
        listener.onSuccess(it)
    }.addOnFailureListener {
        Log.e("TAG", "uploadFile: ", it)
        listener.onFailure(it)
    }
}
<span class="pln">
</span></code></pre>
<p><strong>8. Create storage reference with the file name of selected item to be downloaded. Specify the file path to download the file on mobile phone.</strong></p>
<pre><div id="copy-button17" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>  
//TODO download the item selected from the recyclerview
fun downloadFile(fileName:String,context:Context){
    val ref = storageManagement.storageReference.child("images/$fileName")
    val downloadPath = Environment.getExternalStorageDirectory().absolutePath + "/Cloud-Storage";
    val file = File(downloadPath,fileName)
    val downloadTask = ref.getFile(file)
    downloadTask.addOnSuccessListener(OnSuccessListener {
        Log.d("TAG", "downloadFile: success")
    }).addOnFailureListener(OnFailureListener {
        Log.e("TAG", "downloadFile: ",it)
    })
}
<span class="pln">
</span></code></pre>
<p><strong>9. Create storage reference with the file name of selected item to be downloaded. Specify the file path to download the file on mobile phone.</strong></p>
<pre><div id="copy-button17" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>  
//TODO get the metadata of the item selected from the recyclerview
fun getMetaData(fileName: String): Long? {
    var ref = storageManagement.storageReference.child("images/$fileName")
    val metadataTask = ref.fileMetadata
    return Tasks.await(metadataTask).size
}
<span class="pln">
</span></code></pre>

