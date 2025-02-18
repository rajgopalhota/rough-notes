import React, { useState } from "react";
import { Modal, ModalHeader, ModalBody, ModalFooter, Button } from "reactstrap";
import { toast } from "react-toastify";

const images = [
  "/images/image1.jpg",
  "/images/image2.jpg",
  "/images/image3.jpg",
  "/images/image4.jpg",
  "/images/image5.jpg",
];

interface ImageSelectionModalProps {
  isOpen: boolean;
  toggle: () => void;
  onSelect: (imageUrl: string) => void;
}

const ImageSelectionModal: React.FC<ImageSelectionModalProps> = ({ isOpen, toggle, onSelect }) => {
  const [selectedImage, setSelectedImage] = useState<string | null>(null);
  const [isMinimized, setIsMinimized] = useState(false);

  const handleSubmit = () => {
    if (!selectedImage) {
      toast.error("Please select an image!");
      return;
    }
    onSelect(selectedImage);
    toast.success("Image selected successfully!");
    toggle();
  };

  return (
    <>
      {!isMinimized && (
        <Modal isOpen={isOpen} toggle={toggle} centered size="md" className="shadow-lg rounded-lg">
          <ModalHeader toggle={toggle} className="bg-primary text-white font-bold">
            Select an Image
            <Button color="secondary" size="sm" className="ml-3" onClick={() => setIsMinimized(true)}>Minimize</Button>
          </ModalHeader>
          <ModalBody className="flex flex-wrap justify-center gap-4 p-4">
            {images.map((img, index) => (
              <img
                key={index}
                src={img}
                alt={`Preview ${index + 1}`}
                className={`w-24 h-24 rounded-lg shadow-md cursor-pointer border-4 transition-all ${
                  selectedImage === img ? "border-blue-600 scale-105" : "border-gray-300"
                }`}
                onClick={() => setSelectedImage(img)}
              />
            ))}
          </ModalBody>
          <ModalFooter className="flex justify-between">
            <Button color="secondary" onClick={toggle}>Cancel</Button>
            <Button color="primary" onClick={handleSubmit}>Submit</Button>
          </ModalFooter>
        </Modal>
      )}
      {isMinimized && (
        <div className="fixed bottom-4 right-4 bg-white shadow-lg p-3 rounded-md">
          <Button color="primary" onClick={() => setIsMinimized(false)}>Restore Modal</Button>
        </div>
      )}
    </>
  );
};

export default ImageSelectionModal;



import React, { useState } from "react";
import { Button, Form, FormGroup, Label, Input } from "reactstrap";
import ImageSelectionModal from "./ImageSelectionModal";

const CreateUpdateUser: React.FC = () => {
  const [isModalOpen, setIsModalOpen] = useState(false);
  const [selectedImage, setSelectedImage] = useState<string | null>(null);
  const [userData, setUserData] = useState({
    name: "",
    email: "",
    profilePic: "",
  });

  const handleImageSelect = (imageUrl: string) => {
    setSelectedImage(imageUrl);
    setUserData({ ...userData, profilePic: imageUrl });
  };

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setUserData({ ...userData, [e.target.name]: e.target.value });
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    console.log("User Data Submitted:", userData);
  };

  return (
    <div className="p-5 bg-light shadow rounded">
      <h3 className="text-primary mb-4">Create / Update User</h3>
      <Form onSubmit={handleSubmit}>
        <FormGroup>
          <Label>Name</Label>
          <Input type="text" name="name" value={userData.name} onChange={handleChange} required />
        </FormGroup>
        <FormGroup>
          <Label>Email</Label>
          <Input type="email" name="email" value={userData.email} onChange={handleChange} required />
        </FormGroup>
        <FormGroup>
          <Label>Profile Picture</Label>
          <div className="d-flex align-items-center">
            {selectedImage && <img src={selectedImage} alt="Selected" className="w-16 h-16 rounded-circle mr-3" />}
            <Button color="info" onClick={() => setIsModalOpen(true)}>Choose Image</Button>
          </div>
        </FormGroup>
        <Button color="success" type="submit">Submit</Button>
      </Form>

      <ImageSelectionModal isOpen={isModalOpen} toggle={() => setIsModalOpen(!isModalOpen)} onSelect={handleImageSelect} />
    </div>
  );
};

export default CreateUpdateUser;