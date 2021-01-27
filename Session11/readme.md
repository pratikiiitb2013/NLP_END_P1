
# Encoder part. Following are main points to understand :

* ##### We will be using a CNN(where al of the data goes simulultaneosly at once) and not RNN( where input data goes sequentially). So we need some mechanism to tell the network position of each word.
* ##### For this, we need to add aposition vector in addition to word vector. These word and position vectors will be transformed into seperate embeddings and then added through element wise sum.
* ##### First, input sentence is going to be padded. This is because conv. layer is going to reduce the length of input sentence ( which is the way convoutions work) but we want to make sure that the input and output remains same length.
* ##### Another important point is, we are going to be using GLU activation function. Basically it takes input, divide into 2 parts and apply sigmoid to one and tanh to another and then do an element wise sum. So the output of GLU activation will be reduced to half of input.
* ##### So to keep input and output dimension same with respect to CNN, we will double the output channels from CNN, because after CNN it has to go through GLU activation, where again it will be halved.
* ##### We will also be adding a skip connection from before conv block to output of conv block, because what if there is some issue with conv layer and is not training properly and gradients can't flow, then we need to skip the conv. layer. Therefore, it will allow our NN to handle the gradients.
* ##### Now, the conv output and skipped embeddings wil be added and is called residual connection. This residual connection from one conv layer is the input to next conv layer and so on and on. Next layer will follow the same procedure, add padding -> conv layer -> GLU activation -> skip connection -> residual connection.
* ##### In Encoder network definition, while defining position embedding, we need to specify a "max_length", which is the max length of a sentence our network can handle, basically it is like network capacity.
* ##### Before inputting to conv layer, we have to rearrange the input data from [batch_size, src_len,  hid_dim] to [batch_size, hid_dim, src_len] because that is the requirement from CNN layer. We use permute for this change.
