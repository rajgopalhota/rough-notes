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
    <Modal isOpen={isOpen} toggle={toggle} centered size="md" className="shadow-lg rounded-lg">
      <ModalHeader toggle={toggle} className="bg-blue-600 text-white font-bold">Select an Image</ModalHeader>
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
  );
};

export default ImageSelectionModal;




import React, { useState } from "react";
import ImageSelectionModal from "./ImageSelectionModal";
import { Button } from "reactstrap";

const App = () => {
  const [isModalOpen, setIsModalOpen] = useState(false);
  const [selectedImage, setSelectedImage] = useState<string | null>(null);

  const handleSelectImage = (imageUrl: string) => {
    console.log("Selected Image URL:", imageUrl);
    setSelectedImage(imageUrl);
  };

  return (
    <div className="flex flex-col items-center justify-center min-h-screen bg-gray-100 p-5">
      <h2 className="text-2xl font-bold mb-4">Select Your Profile Image</h2>
      {selectedImage && <img src={selectedImage} alt="Selected" className="w-32 h-32 rounded-full shadow-lg mb-4" />}
      <Button color="primary" onClick={() => setIsModalOpen(true)}>Choose Image</Button>

      <ImageSelectionModal 
        isOpen={isModalOpen} 
        toggle={() => setIsModalOpen(!isModalOpen)} 
        onSelect={handleSelectImage} 
      />
    </div>
  );
};

export default App;