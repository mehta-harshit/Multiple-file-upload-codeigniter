#in View
<input name="images[]" type="file" multiple="" id="images" />
#in controller
$imageCount = count($_FILES['images']['size']);
// die(var_dump($imageCount);
foreach($_FILES as $key=>$value){
$uploadImage=array();
for($i = 0; $i < $imageCount; $i++){
$_FILES['userFile']['name'] = $value['name'][$i];
$_FILES['userFile']['type'] = $value['type'][$i];
$_FILES['userFile']['tmp_name'] = $value['tmp_name'][$i];
$_FILES['userFile']['error'] = $value['error'][$i];
$_FILES['userFile']['size'] = $value['size'][$i];

$uploadPath =FCPATH . 'uploads/products/images/';
$config['upload_path'] = $uploadPath;
$config['allowed_types'] = 'gif|jpg|png|GIF|PNG|JPG|jpeg|JPEG';
$config['encrypt_name'] = TRUE;

$this->load->library('upload', $config);
$this->upload->initialize($config);
$this->upload->do_upload('userFile');
if (!$this->upload->do_upload())
{
   die(json_encode(array("success"=>"FALSE","message"=>strip_tags($this->upload->display_errors()))));
}
else{
$fileData = $this->upload->data();
$uploadImage[$i]['images'] = $fileData['file_name'];
$uploadImage[$i]['productId'] = $productId; 
}
}
if(!empty($uploadImage)){
$insertImages = $this->Product_model->insertMedia($uploadImage);
if($insertImages==FALSE)
{
    $data['success']="FALSE";
    $data['message']="Some problem.Product Image not added right now.";   
    die(json_encode($data));
}
}
}
#in Model
public function insertMedia($data = array()){
            $insert = $this->db->insert_batch('productimages',$data);
            return $insert?TRUE:FALSE;
        }