
# Encoder part. Following are main points to understand:
--------------------------------------------------------

* ##### We will be using a CNN(where al of the data goes simulultaneosly at once) and not RNN( where input data goes sequentially). So we need some mechanism to tell the network position of each word.
* ##### For this, we need to add aposition vector in addition to word vector. These word and position vectors will be transformed into seperate embeddings and then added through element wise sum.
* ##### First, input sentence is going to be padded. This is because conv. layer is going to reduce the length of input sentence ( which is the way convoutions work) but we want to make sure that the input and output remains same length.
* ##### Another important point is, we are going to be using GLU activation function. Basically it takes input, divide into 2 parts and apply sigmoid to one and tanh to another and then do an element wise sum. So the output of GLU activation will be reduced to half of input.
* ##### So to keep input and output dimension same with respect to CNN, we will double the output channels from CNN, because after CNN it has to go through GLU activation, where again it will be halved.
* ##### We will also be adding a skip connection from before conv block to output of conv block, because what if there is some issue with conv layer and is not training properly and gradients can't flow, then we need to skip the conv. layer. Therefore, it will allow our NN to handle the gradients.
* ##### Now, the conv output and skipped embeddings wil be added and is called residual connection. This residual connection from one conv layer is the input to next conv layer and so on and on. Next layer will follow the same procedure, add padding -> conv layer -> GLU activation -> skip connection -> residual connection.
* ##### In Encoder network definition, while defining position embedding, we need to specify a "max_length", which is the max length of a sentence our network can handle, basically it is like network capacity.
* ##### Before inputting to conv layer, we have to rearrange the input data from [batch_size, src_len,  hid_dim] to [batch_size, hid_dim, src_len] because that is the requirement from CNN layer. We use permute for this change.



# Decoder part. Following are main points to understand :
---------------------------------------------------------

* ##### Decoder is similar to encoder part with few changes.
* ##### Decoder will take the 2 outputs from encoder(conved and combined)
* ##### It differs from earlier studied RNN decoder networks, because there is no sequence here, and all predictions will be done in simultaneously in parallel.
* ##### One input to the decoder is sequence to be predicted. Now this is possible at training time but not at inference time. So we need to do some manipulations in order to acheive this.(We will see this in further below  in inference explaination)
* ##### Aother difference here from encoder is, there is no skip connection directly to last block as in encoder, instead embeddings are fet to conv block.
* ##### <b>Conv. Block</b> : It is slightly different from encoder conv. block. In encoder conv block we have uniform padding on left and right, while in decoder conv block we will be padding only in the beggining of the sentnce.
  * This is done because we we don't pad only in the beginning, then due to the conv kernel span, it will take the word which we are trying to predict as input and will just learn to copy paste.
  * For example, in our case kernel size is 3. Now if we add a padding of 1 in beginning and in last, then input to conv layer is n-1th word, nth word and n+1th to predict n+1th word. This is not what we want. So basically by introducing 2 paddings at the beginning, we right shifted twice, so that the input to conv is n-2th, n-1th and nth word to predict n+1th word.
* ##### <b>Attention</b> : We will also be using attention in out code. 
  * We will be using both hid_to_embedded and embeddded_to_hid attention.
  * Attention layers are similar to what we have learnt earlier in RNN sessions.
  * We will be finding attention on both conved and combined(comig from encoder output).
  * We are using both because we want to figure out where exactly the network wants us to put the attention on, combined or only conved part.
* ##### <b>Inference</b> : Now, during inference we don't have target sequence as we have during training part. But decoder needs that so we have to generate that. Now, how we are doing that, is by running code in loop of max length, we start with <I>init_token</I> and send only its index( as target index) in first iteration of the loop. We get the prediction(predicted token) and append it's token to the previous target index and again get the prediction. Therefore, the elements of target sequence are the generated predictions till that point of time. 
