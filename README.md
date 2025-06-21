# Stenography-hiding-information-in-the-image-

from PIL import Image

def genData(data):
    return [format(ord(i), '08b') for i in data]

def modPix(pix, data):
    datalist = genData(data)
    lendata = len(datalist)
    imdata = iter(pix)
    
    for i in range(lendata):
        pixels = [value for value in next(imdata)[:3] + next(imdata)[:3] + next(imdata)[:3]]
        
        for j in range(8):
            if datalist[i][j] == '0' and pixels[j] % 2 != 0:
                pixels[j] -= 1
            elif datalist[i][j] == '1' and pixels[j] % 2 == 0:
                pixels[j] = pixels[j] - 1 if pixels[j] != 0 else pixels[j] + 1
        
        if i == lendata - 1:
            pixels[-1] |= 1 
        else:
            pixels[-1] &= ~1  
        
        yield tuple(pixels[:3])
        yield tuple(pixels[3:6])
        yield tuple(pixels[6:9])

def encode_enc(newimg, data):
    w = newimg.size[0]
    (x, y) = (0, 0)
    
    for pixel in modPix(newimg.getdata(), data):
        newimg.putpixel((x, y), pixel)
        x = 0 if x == w - 1 else x + 1
        y += 1 if x == 0 else 0

def encode():
    img = input("Enter image name (with extension): ")
    image = Image.open(img, 'r')
    data = input("Enter data to be encoded: ")
    
    if not data:
        raise ValueError("Data is empty")
    
    newimg = image.copy()
    encode_enc(newimg, data)
    new_img_name = input("Enter the name of new image (with extension): ")
    newimg.save(new_img_name, new_img_name.split(".")[-1].upper())

def decode():
    img = input("Enter image name (with extension): ")
    image = Image.open(img, 'r')
    imgdata = iter(image.getdata())
    data = ""
    
    while True:
        pixels = [value for value in next(imgdata)[:3] + next(imgdata)[:3] + next(imgdata)[:3]]
        binstr = ''.join(['1' if i % 2 else '0' for i in pixels[:8]])
        data += chr(int(binstr, 2))
        
        if pixels[-1] % 2 != 0:
            break
    
    return data

def main():
    choice = input(":: Welcome to Steganography ::\n1. Encode\n2. Decode\n")
    if choice == '1':
        encode()
    elif choice == '2':
        print("Decoded Word: " + decode())
    else:
        print("Invalid choice, exiting.")

        if __name__=="__main__":
        main()
